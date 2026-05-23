---
title: Vercel
summary: Install FastYoke from the Vercel Marketplace, or sign up directly and link an existing Vercel project. Both paths end at the same tenant.
---

# Vercel

FastYoke supports two paths to a working tenant:

- **Direct signup** — visit `/signup`, create an account, and get a
  Hobby tenant immediately.
- **Vercel Marketplace install** — add FastYoke from the Vercel
  integration catalog; the install saga provisions the org, tenant,
  and API credentials automatically, then emails you an activation
  link.

Both paths converge on the same tenant shape: a FastYoke admin
account, a scoped `fy_pat_*` API token, and four environment
variables written into your linked Vercel project.

---

## Marketplace install path

### 1. OAuth consent

When you install FastYoke from the Vercel integration catalog,
Vercel redirects your browser to:

```
GET /vercel/oauth/authorize
```

This page displays a region selector (the FastYoke instance your
data will be bound to). Submitting the form signs an OAuth state
nonce and redirects to Vercel's approval URL.

### 2. Install saga

After you approve the Vercel consent screen, Vercel calls the
registered callback:

```
GET /vercel/oauth/callback
```

The callback exchanges the authorization code for an access token,
stores it encrypted, and starts a provisioning saga. The saga
advances through these states in order:

```
pending
  → org_created
  → tenant_created
  → db_seeded
  → token_minted
  → env_injected         ← env vars written to your Vercel project
  → webhooks_registered  ← lifecycle webhooks registered
  → complete             ← API key is live
  → user_invited         ← activation email sent (terminal happy-path state)
```

During provisioning an interstitial page polls
`GET /vercel/oauth/status?saga=<id>` for progress. If the saga
stalls or errors it transitions to `failed`; the LIFO rollback
removes every artifact created so far (token → tenant → org) and
leaves the platform clean for a retry.

### 3. Env-var injection

When the saga reaches `env_injected`, four environment variables are
written across **production, preview, and development** environments
on your linked Vercel project:

| Variable | Type | Value |
|---|---|---|
| `FASTYOKE_API_KEY` | encrypted | `fy_pat_*` bearer token — scoped to your tenant |
| `FASTYOKE_TENANT_ID` | plain | Your tenant UUID |
| `FASTYOKE_API_BASE` | plain | `https://api.fastyoke.com` |
| `FASTYOKE_WEBHOOK_SECRET` | encrypted | Per-tenant HMAC-SHA256 signing secret for inbound webhooks |

`FASTYOKE_API_KEY` and `FASTYOKE_WEBHOOK_SECRET` are stored as
Vercel **encrypted** variables — readable by your deployments at
runtime but not visible in the Vercel dashboard UI. The other two
are **plain** (visible in the dashboard).

Injection is idempotent: the saga lists existing env-var keys
first and skips any that are already present, so retries and
project-rename re-injections never create duplicates.

### 4. Activation email

After `complete`, the saga calls `GET https://api.vercel.com/v2/user`
to retrieve the installing user's email, then sends a welcome email
containing a one-time activation link:

```
https://app.fastyoke.com/vercel-claim/<token>
```

The claim token is valid for **7 days**. Clicking the link:

- **New FastYoke account** — presents a password-set form. On
  submit, your account is activated, a JWT is issued, and you land
  at `/admin`.
- **Existing FastYoke account** — shows a login prompt. Signing in
  silently attaches the new Vercel tenant to your existing account;
  no separate claim step required.

::callout{type="warn" title="Activation link is single-use"}
Each link can only be consumed once. If yours has expired or was
already used, ask your platform operator to resend it (see the
operator section below). The link cannot be regenerated from the
dashboard — only via the CLI.
::

---

## Direct signup path

Visit `/signup` to create a FastYoke account without going through
the Vercel Marketplace. You receive a Hobby tenant immediately and
can start building. To link a Vercel project later, add the four env
vars from the table above manually using `fy_pat_*` tokens you
generate at `/admin/settings`.

---

## After activation

Once your account is active, the admin shell is at **`/admin`**.
Key surfaces for Vercel-connected tenants:

- `/admin/settings` — tenant settings, branding, plan tier.
- `/admin/settings` (API Tokens tab) — manage `fy_pat_*` tokens.
- `/admin/integrations` — outbound webhook credentials (Twilio,
  SendGrid, generic REST).

---

## Lifecycle events

FastYoke registers lifecycle webhooks with Vercel during the
`webhooks_registered` saga step. The following events are handled
automatically:

| Event | Effect |
|---|---|
| `project.renamed` | Re-runs env injection idempotently so your renamed project still carries all four vars. |
| `project.deleted` | Soft-deletes the installation row. The tenant and its data are preserved. |
| `integration.uninstalled` | Soft-deletes the installation and revokes the linked `FASTYOKE_API_KEY`. The tenant, org, and all tenant data are preserved — you can reconnect by reinstalling. |
| `project.transferred` | Refused (returns an error). Transfers are not supported. |

::callout{type="info" title="Uninstall preserves your data"}
Removing FastYoke from a Vercel project does not delete your tenant
database, FSM schemas, jobs, or event log. It only revokes the API
token and unlinks the installation. Reinstalling provisions a
fresh token and re-injects env vars into the new project.
::

---

## Token rotation

Platform operators can rotate the `FASTYOKE_API_KEY` written into
your Vercel project without downtime:

```
POST /vercel/installations/<installation_id>/rotate
{ "reason": "<audit note>" }
```

The rotation:
1. Mints a fresh `fy_pat_*` token.
2. Pushes it to Vercel as the new `FASTYOKE_API_KEY` env var.
3. Revokes the prior token.

If step 2 (the Vercel push) fails, step 1 is rolled back so the old
token stays live — your project never has a broken API key. Rotation
is platform-admin gated; it cannot be triggered from the tenant
dashboard.

---

## Operator reference

### Environment variables (server-side)

The Vercel integration is disabled by default. Set all three to
enable `/vercel/*` routes:

| Variable | Description |
|---|---|
| `VERCEL_CLIENT_ID` | OAuth2 client ID from the Vercel partner dashboard. |
| `VERCEL_CLIENT_SECRET` | OAuth2 client secret. Also used to verify HMAC-SHA1 signatures on incoming lifecycle webhooks. |
| `VERCEL_REDIRECT_URI` | Registered callback URL, e.g. `https://api.fastyoke.com/vercel/oauth/callback`. |

All three must be set; any missing value disables every `/vercel/*`
route (returns 503).

### Saga reconciliation CLI

If a saga stalls or the activation email needs to be resent, use the
`fastyoke-admin` binary:

```sh
# Inspect a saga (read-only, safe on any id)
fastyoke-admin vercel-saga-reconcile \
  --id <saga-id> \
  --action dry

# Drive a db_seeded saga to token_minted
fastyoke-admin vercel-saga-reconcile \
  --id <saga-id> \
  --action complete

# Resend the activation email (requires --confirm matching --id)
fastyoke-admin vercel-saga-reconcile \
  --id <saga-id> \
  --action user-invite \
  --confirm <saga-id>

# LIFO rollback — removes all artifacts (requires --confirm)
fastyoke-admin vercel-saga-reconcile \
  --id <saga-id> \
  --action force-rollback \
  --confirm <saga-id>
```

`dry` is the safe default and requires no `TENANT_DATA_DIR`.
`force-rollback` requires `TENANT_DATA_DIR` to be set (same as the
running app) so it can remove the per-tenant SQLite file.

The saga sweeper worker automatically flips stale `pending` sagas to
`failed` every 30 seconds, keeping the platform clean without manual
intervention.
