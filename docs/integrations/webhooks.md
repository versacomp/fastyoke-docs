---
title: Webhooks
description: Receive HTTPS POSTs when events happen in your FastYoke tenant.
---

# Webhooks

FastYoke can POST HTTPS callbacks to your endpoint whenever a job transitions,
a form is submitted, or an entity is created, updated, or deleted. Every
request is signed with HMAC-SHA256 so you can verify it originated from
FastYoke and was not tampered with in transit.

## Event catalog

Subscribe to one or more of the following event types. The `*` wildcard
matches every event.

| Event                | Fires when                                         |
|----------------------|----------------------------------------------------|
| `job.transition`     | A workflow job moves to a new state.               |
| `job.admin_override` | An admin force-overrides a job to a target state.  |
| `form.submitted`     | A public form is submitted.                        |
| `entity.created`     | A tenant entity record is created.                 |
| `entity.updated`     | A tenant entity record is updated.                 |
| `entity.deleted`     | A tenant entity record is deleted.                 |

## Request envelope

Every delivery is an HTTPS `POST` with a JSON body in the following shape:

```json
{
  "id": "wh_evt_01HX...",
  "event": "job.transition",
  "occurred_at": "2026-06-17T14:22:01Z",
  "tenant_id": "tnt_01HW...",
  "data": {
    "job_id": "job_01HX...",
    "from_state": "Onboarding",
    "to_state": "Active",
    "event_name": "go_live"
  }
}
```

- `id` — globally unique delivery id. Stable across retries; use it for
  idempotency on your side.
- `event` — one of the event types from the catalog above.
- `occurred_at` — RFC 3339 timestamp the event was recorded.
- `tenant_id` — your tenant's id.
- `data` — event-specific payload. The shape varies per event type.

## Headers

Every delivery includes the following headers:

| Header                   | Meaning                                                     |
|--------------------------|-------------------------------------------------------------|
| `Content-Type`           | Always `application/json`.                                  |
| `User-Agent`             | `FastYoke-Webhooks/1.0`.                                    |
| `X-FastYoke-Event`       | The event type (e.g. `job.transition`).                     |
| `X-FastYoke-Delivery`    | Unique delivery id — equals the `id` field in the body.     |
| `X-FastYoke-Timestamp`   | Unix epoch seconds when the request was signed.             |
| `X-FastYoke-Signature`   | `sha256=<hex>` HMAC-SHA256 of the raw request body.         |
| `X-FastYoke-Attempt`     | Attempt number, starting at `1` on first delivery.          |

## Verifying signatures

The signature is `sha256=` followed by the lowercase hex HMAC-SHA256 of the
raw request body, using your subscription's signing secret as the key.
Compare with a constant-time function — never with `==`.

### Node.js

```js
import { createHmac, timingSafeEqual } from 'node:crypto';

export function verify(secret, rawBody, signatureHeader) {
  const expected =
    'sha256=' + createHmac('sha256', secret).update(rawBody).digest('hex');
  const a = Buffer.from(expected);
  const b = Buffer.from(signatureHeader);
  return a.length === b.length && timingSafeEqual(a, b);
}
```

### Python

```python
import hmac
from hashlib import sha256

def verify(secret: bytes, raw_body: bytes, signature_header: str) -> bool:
    expected = "sha256=" + hmac.new(secret, raw_body, sha256).hexdigest()
    return hmac.compare_digest(expected, signature_header)
```

### Ruby

```ruby
require 'openssl'

def verify(secret, raw_body, signature_header)
  expected = 'sha256=' + OpenSSL::HMAC.hexdigest('SHA256', secret, raw_body)
  Rack::Utils.secure_compare(expected, signature_header)
end
```

Always compute the HMAC over the **raw** request body bytes, before any JSON
parsing or re-serialization. If your framework re-serializes the body before
your handler sees it, the bytes will differ and verification will fail.

## Retry behavior

If your endpoint returns a non-2xx response or fails to respond within the
delivery timeout, FastYoke retries on a backoff schedule:

| Attempt   | Delay before next attempt |
|-----------|---------------------------|
| 1 → 2     | 1 minute                  |
| 2 → 3     | 5 minutes                 |
| 3 → 4     | 30 minutes                |
| 4 → 5     | 2 hours                   |
| 5         | → dead-letter             |

A delivery is considered successful on any `2xx` response. After the fifth
failed attempt the delivery moves to the dead-letter log, where you can
inspect the response code, body, and last error.

After 10 consecutive dead-letter deliveries on the same subscription, the
subscription auto-pauses to protect your endpoint from continued retries.
Re-enable it from the Webhooks settings page after fixing the underlying
issue.

Because retries can occur after transient failures, your handler must be
idempotent. Use `X-FastYoke-Delivery` (equal to the body's `id`) as the
idempotency key — it is stable across all retries of the same event.

## Tier caps

Outbound webhooks are available on every paid plan. The number of active
subscriptions per tenant scales with your plan:

| Plan        | Active subscriptions |
|-------------|----------------------|
| Hobby       | 1                    |
| Pro         | 5                    |
| Business    | 25                   |
| Enterprise  | Unlimited            |

Paused subscriptions do not count toward the cap.
