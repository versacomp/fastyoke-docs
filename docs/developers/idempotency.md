---
title: Idempotency
summary: Which FastYoke endpoints accept Idempotency-Key, how the platform dedupes on it, and what the safe-retry window is.
order: 4
---

# Idempotency

Network retries can produce duplicate side effects — double-charge,
double-webhook, double-push. Endpoints that mutate state in ways
the platform can't easily undo accept an `Idempotency-Key` header
so a safe retry is identical to the first call.

## Which endpoints support `Idempotency-Key`

Three endpoint families today:

- **Outbound webhook delivery** — server-side. Each delivery carries
  a stable `X-FastYoke-Delivery-Id` header. **Receivers** should
  dedupe on that id: if the same delivery id arrives twice, return
  the same response and skip the side effect.
- **Cloud-storage push** — `POST /tenant/storage/push` to OneDrive
  or Dropbox. Accepts an `Idempotency-Key` header; the platform
  records the key in its push ledger (`storage_push_log`) and
  returns the original push result on a duplicate key within the
  window.
- **Stripe wallet top-up** — the self-serve top-up endpoint accepts
  the header and dedupes within the documented window. Without a
  key, a retried top-up could double-charge.

Other endpoints don't accept `Idempotency-Key` today. See
[What's NOT idempotent](#whats-not-idempotent) below.

## How to choose a key

Any opaque string up to 64 bytes. Two common patterns:

- **UUID v4** — fresh on every operation. Best when you want to
  retry a single specific call.
- **Deterministic hash of the operation payload** — same input,
  same key. Best when your client may produce the same request
  shape twice (e.g., a webhook ingest that you replay).

Choose the pattern that matches your retry behaviour. UUIDs are
the safer default.

## What the platform does with it

1. The first request with a given key runs end-to-end and persists
   its result.
2. The same key arriving again within the window returns the
   original response — the side effect runs exactly once.
3. After the window passes the key is forgotten. A retry with the
   same key after the window will be treated as a fresh request.

The window is documented per endpoint (typically minutes for
top-ups, hours for storage pushes). The platform does not promise
indefinite dedupe — for long-lived idempotence, the receiver of
side effects should dedupe on its own id (e.g., outbound webhook
receivers dedupe on `X-FastYoke-Delivery-Id`).

## What's NOT idempotent

Anything not on the list above. In particular, a plain `POST` to
create an entity is **not** idempotent — duplicate posts create
duplicate entities. If your client retries entity creates, supply
your own dedupe layer (e.g., a unique field constraint) or fetch
before posting.

The platform extends idempotency support over time as new
endpoints are identified. This page lists what's supported today.
