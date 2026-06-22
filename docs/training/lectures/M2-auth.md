---
title: Lecture M2 — Auth & token layers
summary: Why the platform ships seven distinct auth-token shapes, and how the one-hour admin test JWT differs from a fy_pat_ PAT.
order: 3
---

# Lecture M2 — Auth & token layers

## The mental model

The auth surface has many token shapes because the same
identity expresses itself differently across browsers,
servers, CI, and partner integrations. Each token shape is
designed for one realistic use; mixing them produces the
kind of confusion that motivates "just put it all in a
single bearer token" — a temptation the platform
deliberately resists.

## Key concepts

- **Session JWT.** Issued at login (cookies for the SPA).
  Carries the user, current tenant, role, and a short
  expiry.
- **`fy_pat_` PAT.** Long-lived tokens minted by an admin
  for machine-to-machine use. Each PAT has scopes —
  hard-refused if it tries to perform an action outside
  its scopes, even if the user could.
- **One-hour test JWT.** A convenience token an admin
  mints via the tenant settings to run ad-hoc API calls
  from `curl` or Postman. Mirrors the caller's authority,
  not delegated.
- **WorkOS AuthKit session.** SSO flow; the callback
  resolves which tenant the federated identity belongs to.
- **Extension scopes vs PAT scopes.** Drawn from the
  **same** 72-permission catalog. Authoring once means
  the permission you grant a PAT means exactly the same
  thing if you ever ship an extension.
- **Anti-delegation refusal.** A `fy_pat_` token cannot
  impersonate other users — the platform returns
  `delegated_credential_refused` rather than silently
  acting as someone else.

## Common pitfalls

- **Sharing a PAT in source control.** PATs are
  long-lived; one leaked PAT can drain a billing wallet.
  Rotate aggressively; revoke on departure.
- **Assuming SSO replaces the rest.** WorkOS replaces the
  password flow, not PATs and not the test JWT.
- **Confusing role with scope.** Role gates the UI
  surfaces; scope gates the API. A PAT-bearing client
  with `data:read` scope can't call a `data:write`
  endpoint even if the underlying user is admin.

## Where to go next

- Lab: [M2 — Token walkthrough](/docs/training/labs/M2-token-walkthrough).
- Reference: [/docs/auth](/docs/auth) and [/docs/auth/api-tokens](/docs/auth/api-tokens).
