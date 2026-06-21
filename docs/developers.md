---
title: Developers
summary: Day-1 platform invariants every API consumer needs to know, plus links to every developer surface FastYoke ships.
order: 1
---

# Developers

If you're calling the FastYoke API or building on top of the
platform, start here. The four pages below are the day-1 platform
invariants every API consumer needs to know; the link clusters
underneath route you to whichever developer surface you came for.

## Day-1 invariants

- [Error envelope](/docs/developers/errors) — what every 4xx and
  5xx response looks like, and what each status code means in
  FastYoke specifically.
- [Tenant scoping](/docs/developers/tenant-scoping) — how the
  platform enforces multi-tenancy and what your code does (and
  doesn't) send.
- [Idempotency](/docs/developers/idempotency) — making safe retries
  on the endpoints that support `Idempotency-Key`.
- [Rate limits](/docs/developers/rate-limits) — what the platform
  throttles, the 429 response shape, and recommended client
  behaviour.

## Building on the platform

- [SDK](/docs/sdk) — typed client for JavaScript / TypeScript.
- [CLI](/docs/cli) — `fastyoke` command-line operations.
- [Extensions](/docs/extensions) — ship your own React UI mounted
  inside the admin shell.
- [Integrations](/docs/integrations) — inbound webhooks and
  outbound webhook subscriptions.
- [LCAP](/docs/lcap) — low-code app platform primitives (annotations,
  field types, formatting).
- [Primitives](/docs/primitives) — server-side compute building
  blocks (aggregator, scheduler, pdf-generator, and more).
- [Recipes](/docs/recipes) — end-to-end how-to guides.

## Per-feature API references

Feature-specific API documentation lives next to the admin pages
those features ship with:

- [Messaging API](/docs/messaging/api-reference) — REST endpoints
  for conversations and messages, plus the WebSocket envelope.
- [E-signatures public verify](/docs/esign/trust-and-verify) — the
  offline-verification recipe and the published ed25519 public
  keys.

If this is your first time, the ten-minute
[first-integration tutorial](/docs/tutorials/101-first-integration)
is the fastest way to confirm the contract works
end-to-end against your tenant.
