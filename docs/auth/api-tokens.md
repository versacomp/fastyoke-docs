---
title: API tokens
summary: Long-lived fy_pat_ bearer tokens for CI pipelines and external integrations — minting, format, scopes, hard refusals, revocation, and the rotation pattern.
order: 3
---

# API tokens

An API token is a long-lived bearer credential a server-side
caller uses to authenticate against the tenant API. Every
token starts with the prefix `fy_pat_` and is bound to
exactly one tenant.

## When to use one

- **CI pipelines** that run schema syncs, seed entity
  records, or fire workflow events as part of a deploy.
- **Server-side integrations** that call the tenant API
  on behalf of the tenant without a human present.
- **One-off scripts** for bulk operations a human admin
  would normally do interactively.

**Don't use one for human sign-in.** Humans go through the
[sign-in flows](/docs/auth/signin-flows) and get a
short-lived tenant JWT. API tokens are the long-lived
shape — appropriate only for non-interactive callers.

## Minting

From the admin shell, open **Settings → Tokens** and click
**New token**. You'll pick:

- A **name** (free-form; what shows on the token list).
- A **scope set** from the catalogue. Scope vocabulary is
  documented in [the index](/docs/auth#scopes); tokens
  draw from the same vocabulary as extension JWTs.

Click **Create**. The token value is shown once at create
time and the platform never reveals it again. Copy it
into your CI secret manager immediately.

## Format & display

- Every token starts with `fy_pat_`.
- After creation, the portal shows a prefix-and-suffix
  preview only (e.g., `fy_pat_AbCd…XyZ9`). The full
  token value is not retrievable.
- Under the hood the platform stores a peppered hash, not
  the token itself. There is no "show token" button.

## Hard refusals

API tokens are deliberately weaker than a human admin
session. The four refusals (also stated in the
[authentication index](/docs/auth)):

- A token **cannot install** an extension.
- A token **cannot mint** another API token.
- A token **cannot revoke other** API tokens.
- A token **cannot uninstall** an extension.

A leaked token therefore cannot permanently graft itself
into the tenant. The refusals are enforced at the API
layer; a token-authenticated request to a refusal path
returns 403 immediately.

## Revocation

Revoke a token from **Settings → Tokens → Revoke**, or
hit `DELETE /api/v1/tenant/api-tokens/:id` from a human
admin session. Revocation is immediate — the next request
authenticating with the revoked token returns 401.

## Rotation pattern

Scheduled rotation isn't supported today. The pattern
that works:

1. From a human admin session, mint a new token with the
   same scope set.
2. Deploy the new token to your CI secret store.
3. Confirm CI jobs are passing with the new token.
4. Revoke the old token.

Plan a maintenance window if your CI is single-track —
between step 2 and step 4 either token authenticates.

## REST surface

- `GET /api/v1/tenant/api-tokens` — List active and revoked tokens (prefix-and-suffix preview only).
- `POST /api/v1/tenant/api-tokens` — Mint a new `fy_pat_` token. The response body contains the full token value **once**.
- `DELETE /api/v1/tenant/api-tokens/:id` — Revoke a token. Effect is immediate.
- `POST /api/v1/tenant/api-tokens/test-token` — Mint a one-hour session-mirroring JWT for ad-hoc API testing. See [Quick test token](#quick-test-token).

The mint, revoke, and test-token endpoints all require a
human admin session — calling them with another API token
returns 403 (the "cannot mint, cannot revoke other" refusals
above, and the dedicated `delegated_credential_refused`
refusal for the test-token endpoint).

## Walk-through

For an end-to-end "mint → curl → revoke" walkthrough in a
real CI shell, see
[CI scripting](/docs/recipes/ci-scripting). This page is
the reference; the recipe is the walkthrough.

## What's not in API tokens today

- **Scheduled rotation** — the platform auto-rotates on a
  cadence and emails you the new value.
- **Token expiry windows** — set a hard expiry at mint
  time.
- **Scope-narrowing without revocation** — edit an
  existing token's scopes in place.

## Quick test token

Need to hit the API right now from `curl` or Postman without
the full `fy_pat_` mint ceremony? **Settings → Tokens → Generate test token**
mints a short-lived **JWT bearer** that acts as your admin
session for one hour.

The dialog returns three things you can copy: the token
itself, a ready-made `curl` line, and the absolute expiry
timestamp.

### How it differs from a `fy_pat_` token

A test token is **not** a `fy_pat_` PAT. The differences
are deliberate and load-bearing:

| Property | `fy_pat_` token | Test token (JWT) |
| --- | --- | --- |
| Format | `fy_pat_…` prefix | A standard JWT bearer (`eyJ…`) |
| Authority | The scope set you picked at mint time. The four [hard refusals](#hard-refusals) apply. | **Mirrors your live session** — whatever your admin account can do, the token can do. The hard refusals do **not** apply. |
| Lifetime | Long-lived; rotate or revoke explicitly | **One hour** (`TEST_TOKEN_TTL_SECS = 3600`). Cannot be extended. |
| Storage | Peppered hash stored in `api_tokens` | **Stateless** — nothing is stored. The bearer is returned once and never reproducible. |
| Revocation | `DELETE /api-tokens/:id` is immediate | **Not revocable** — wait for the TTL to expire. |
| Audit | A row per mint and revoke | A single structured `api_test_token_generated` event per mint. The token itself is **never** in the log. |

Because the test token mirrors your authority, treat it
like your password. Don't paste it into Slack, don't commit
it, and don't share it with anyone you wouldn't hand your
session cookie to.

### Anti-delegation

You cannot mint a test token from another `fy_pat_` token
or test token. The endpoint refuses any non-human session
with a `403 delegated_credential_refused` — a leaked PAT
must never be able to spin up an unscoped session-bearing
JWT and bypass its own scope ceiling. The minted bearer
always traces back to a human admin sign-in.

### REST endpoint

`POST /api/v1/tenant/api-tokens/test-token` — admin
session only, requires `tokens.manage`. No request body.
Response shape:

```json
{
  "token": "eyJhbGciOiJI…",
  "token_type": "Bearer",
  "expires_at": "2026-06-21T12:00:00Z",
  "expires_in_secs": 3600,
  "tenant_id": "tnt_01HW…",
  "api_base_url": "https://www.fastyoke.io"
}
```

### When NOT to use one

A test token is for one operator, one terminal, one hour.
For CI pipelines, server-side integrations, anything that
will outlive an interactive session — mint a
`fy_pat_` PAT instead. The `fy_pat_` flavor is revocable,
scope-narrowed, and survives across browser sessions.

## See also

- [Authentication & Scopes](/docs/auth) — the token-layer
  reference.
- [Sign-in flows](/docs/auth/signin-flows) — the
  short-lived human-session shape.
- [CI scripting](/docs/recipes/ci-scripting) — the
  walkthrough.
