---
title: Authentication & Scopes
summary: JWT shapes, platform vs tenant context, extension tokens, SSO.
order: 7
---

# Authentication

Six auth layers. Five are JWT-shaped (tenant user, platform admin,
impersonation, extension, access tokens); the sixth is the
long-lived `fy_pat_*` **API token** for CI pipelines and external
integrations.

## 1. Tenant user JWT

Minted by `POST /auth/login` (local auth) or synthesized at SSO
callback. HS256, 24-hour TTL. Carries:

```ts
{
  sub: string;        // user_id
  email: string;
  tenant_id: string;  // authoritative — backends scope every query
  role: 'admin' | 'operator';
  impersonated_by?: string;  // platform admin email on impersonation
  iat: number;
  exp: number;
}
```

Every `/tenant/*` route validates the JWT and **never trusts**
`tenant_id` from the request body. If a payload carries a
tenant_id that disagrees with the JWT claim, the handler returns
`403 Forbidden`.

## 2. Platform admin JWT

Minted by `POST /super/auth/login`. Separate claim set
(`PlatformClaims`) with no `tenant_id` — platform admins operate
globally.

```ts
{
  sub: string;        // platform_admins.id
  email: string;
  role: 'super_admin';
  iat: number;
  exp: number;
}
```

The `PlatformAdmin` extractor only validates the signature + role.
It does NOT look up the admin in the DB on every request — the
token's HMAC is sufficient proof. This is intentional: lets the
`fastyoke-admin reassign-tenant` CLI mint a platform JWT with a
synthetic `sub` and still authenticate to the ops endpoints, which
is how live tenant handoffs work.

## 3. Impersonation JWT

Mints via `POST /super/impersonate/:tenant_id`. Looks like a
regular tenant JWT but with `impersonated_by` set to the platform
admin's email. 1-hour TTL (short by design — impersonation is a
support tool, not a workflow).

Every action taken under an impersonation token is logged in
`event_log` with the `impersonated_by` email captured, so the audit
trail distinguishes the admin's actions from the real user's.

## 4. Extension JWT

Minted when an extension loads in the admin shell. 15-minute TTL —
compromised bundles have a bounded blast radius. Carries:

```ts
{
  sub: string;        // the user the extension is acting for
  email: string;
  tenant_id: string;
  role: 'admin' | 'operator';
  ext_id: string;     // manifest id, e.g. "acme.shift-heatmap"
  scopes: string[];   // manifest's required_scopes
  iat: number;
  exp: number;
}
```

`ext_id` distinguishes extension JWTs from regular user tokens.
Handlers that want to reject extensions entirely check for its
presence; handlers that care about provenance read it for audit
logging. Scopes are advisory today — `require_scope()` enforcement
lands in a later phase.

## 5. Access tokens (public links)

Not JWTs — opaque strings stored in `access_tokens` (tenant-scoped)
+ `access_token_index` (platform pivot). Used for the public
workspace route: an operator shares a link like
`/public/validate/<token>`, the frontend resolves it, renders a
job-specific view, and the token is automatically bound to a
single `(job_id, entity_id, page_slug)` triple.

## 6. Long-lived API tokens

Opaque bearer credentials for CI pipelines and external
integrations. Mint once at
[Admin → Settings → API Tokens](/admin/settings/api-tokens), copy
the secret exactly once, then use it on the wire:

```bash
curl -H "authorization: Bearer fy_pat_<prefix><secret>" \
  https://fastyoke.example/api/v1/tenant/entities/vehicle?tenant_id=acme
```

**Shape** — `fy_pat_<8-char lookup prefix><secret tail>`. The
`fy_pat_` literal is a deliberate grep-for-me marker (same family
as GitHub's `ghp_`). The prefix is indexable on the platform side
so the auth extractor can find the owning tenant without any
header context; the secret tail is hashed (SHA-256) at rest and
verified in constant time.

**Scopes** — At mint time, the operator picks a subset of the
[scope vocabulary](#scopes). Every request made with the token is
gated by `require_scope()`; a token with only `data:read` cannot
call a `data:write` endpoint. `admin:*` acts as a wildcard for
teams that want a single rotate-me-please key, but narrower grants
are strongly preferred.

**Lifecycle** — `expires_at` defaults to 90 days; operators can
choose 30d / 90d / 1y / never at mint time. Revocation is a
one-way soft-delete: `DELETE /tenant/api-tokens/:id` flips
`revoked_at` and future requests fail closed with 401. The row
stays on the ledger so the audit trail survives ("when was this
token revoked, and by whom?" is always answerable).

**Hard refusals** — API tokens cannot install, mint, uninstall, or
activate extensions; cannot mint further API tokens; cannot revoke
other API tokens. A leaked token must never be able to permanently
graft itself into the tenant, and the refusals are enforced in
`src/api/extensions.rs` and `src/api/api_tokens.rs` as a
belt-and-suspenders pair with the session-only checks.

See the [CI scripting recipe](/docs/recipes/ci-scripting) for the
full mint → curl → revoke walk-through.

## Scopes

The current scope vocabulary:

| Scope | Grants |
|---|---|
| `data:read`, `data:write` | Read / write entity records + schemas. |
| `workflow:read`, `workflow:execute`, `workflow:admin` | Jobs list, transitions, FSM admin. |
| `files:read`, `files:write` | Upload / download entity-file attachments. |
| `forms:read`, `forms:write`, `forms:admin` | Form CRUD (`admin` adds publish / archive / delete). |
| `admin:*` | Wildcard — matches every scope check. Use sparingly. |

These are the scopes that both **extension manifests** and
**API token mints** draw from — adding a new scope is a coordinated
change across `KNOWN_SCOPES` in
`backend/src/api/extensions.rs` and the mint UI.

::callout{type="info" title="Enforcement is live"}
Extension JWTs and API tokens both flow through
  <code>CurrentUser::require_scope()</code>. A missing scope returns
  403 immediately — there is no advisory mode. Human user sessions
  (no <code>ext_id</code>, no <code>api_token_id</code>) bypass
  scope checks; their authority is gated by
  <code>require_admin()</code> instead.
::

## Multi-tenancy invariant

Every SQL statement touching tenant-scoped tables **must** include
`WHERE tenant_id = ?`. This is the platform's
[prime directive](../index.mdx#architecture) — violating it is a
critical security bug. The `CurrentUser` extractor resolves the
per-tenant `SqlitePool` via `tenant_pool_manager.get_pool()` so
handlers can't accidentally query another tenant's data even if
they bind the wrong tenant_id string.

## See also

Authentication establishes who you are. See [Permissions](/docs/permissions) for what you can do.
