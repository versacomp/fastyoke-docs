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

## Managing subscriptions

Outbound webhook subscriptions live at **Settings → Webhooks**
in the admin shell. Each subscription is tenant-scoped; no
subscription crosses tenants.

### Lifecycle states

- **active** — the subscription receives matching events.
- **paused** — the subscription is configured but skipped by
  the dispatcher. Paused subscriptions don't count toward the
  tier cap.

You toggle between the two from the settings list or via
`PATCH /:id`.

### Subscription fields

| Field | Purpose |
| --- | --- |
| `url` | The HTTPS endpoint to POST events to. |
| `events` | A list of event types, or `*` for the wildcard. |
| `description` | Free-form label rendered in the settings list. |
| Signing secret | Generated at create time; rotatable later. Used to compute the HMAC header on every delivery (see [Verifying signatures](#verifying-signatures)). |

### Three event sources

Events come from three places inside the platform:

- **Job transitions** — every FSM transition fires the
  matching `job.transition` or `job.admin_override`.
- **Entity mutations** — create / update / delete on tenant
  entity records fire `entity.created` / `entity.updated` /
  `entity.deleted`.
- **Form submissions** — every public form submission fires
  `form.submitted`.

The dispatcher fans out to every matching subscription per
event. Cross-event ordering across sources is **not**
guaranteed.

## Secret rotation

`POST /:id/rotate-secret` generates a new signing secret for a
subscription. The response body contains the new secret
**once** — the platform never reveals it again. Copy it into
your verifier configuration immediately.

### Dual-window

For a grace period after rotation, both the previous and the new secrets verify deliveries. This lets your receiver
migrate to the new secret without dropping signed bytes.

### Migration pattern

1. Call rotate-secret.
2. Receive the new secret in the response. Update your
   verifier to accept either the old OR new secret during
   the window.
3. Deploy the new secret as your primary verifier.
4. After the grace window expires (or sooner if you're
   confident), retire the old secret from your verifier.

If your verifier only ever checks one secret, rotation is a
**hard cutover** — the new secret takes effect immediately on
the next delivery. The dual-window is opt-in on the
receiver's side; it is not enforced server-side.

## Test deliveries and replay

Three non-event-driven dispatch paths, plus a read endpoint
for inspecting history.

### `POST /:id/test` — ad-hoc test delivery

Fires a synthetic delivery to the subscription with a fixed
`event` type and a fixed body. The test delivery flows
through the **same** dispatcher real events do — same HMAC,
same headers, same DLQ rules. The only difference: it's
triggered manually and doesn't reflect a real tenant event.

Use to smoke-test a new endpoint, verify your HMAC
verification code, or confirm TLS is wired up.

### `POST /deliveries/:delivery_id/replay` — single replay

Looks up a specific past delivery by id and re-fires it. The
replayed body is byte-identical to the original. The HMAC
header re-signs against the **current** secret.

Use after fixing a receiver bug to re-deliver one specific
payload without affecting any other history.

### `POST /:id/replay-dlq` — DLQ batch replay

Re-fires every delivery in the subscription's DLQ in order.
After replay, deliveries that succeed leave the DLQ;
deliveries that fail re-enter the DLQ at the back.

Use for bulk recovery after a receiver-side outage.

### `GET /:id/deliveries` — delivery inspection

Returns the recent delivery history for a subscription: per
delivery, the timestamp, response code, attempt count, and a
truncated copy of the last error message. Read this before
deciding whether to replay.

## SSRF guard

An outbound webhook is a request the platform makes on the
tenant's behalf. A misconfigured (or malicious) subscription
URL could try to probe the platform's internal network. The
SSRF guard refuses those URLs at dispatch time.

### Refused classes

- **Non-HTTPS URLs.** Plain HTTP is not permitted.
- **Loopback addresses** — `127.0.0.0/8` and IPv6 `::1`.
- **Private IPv4 ranges** — `10/8`, `172.16/12`, and
  `192.168/16`. These are the RFC-1918 blocks.
- **Link-local addresses** — `169.254/16` and IPv6
  `fe80::/10`.
- **Unique-local IPv6** — `fc00::/7`.

### When the check runs

The guard checks every dispatch — first attempt and retries.
DNS responses are checked against the same rules. A
hostname that resolves to a forbidden address is refused even
when the hostname itself doesn't look private.

### What the receiver sees

The subscription is marked failed in the delivery log with a refusal message. The refusal **does NOT count toward the 10-consecutive-DLQ auto-pause threshold** — those failures
are for real receiver-side problems, not URLs the platform
refuses to call.

## REST surface

Auth is the tenant JWT; the platform derives `tenant_id`
from the JWT and never accepts it in path or body.

| Method | Path | Purpose |
| --- | --- | --- |
| GET    | `/api/v1/tenant/webhooks` | List subscriptions. |
| POST   | `/api/v1/tenant/webhooks` | Create a subscription. Response body contains the signing secret **once**. |
| GET    | `/api/v1/tenant/webhooks/:id` | Read one subscription. |
| PATCH  | `/api/v1/tenant/webhooks/:id` | Update URL, events, description, or paused/active. |
| DELETE | `/api/v1/tenant/webhooks/:id` | Delete a subscription. |
| POST   | `/api/v1/tenant/webhooks/:id/rotate-secret` | Generate a new signing secret. |
| POST   | `/api/v1/tenant/webhooks/:id/test` | Fire an ad-hoc test delivery. |
| GET    | `/api/v1/tenant/webhooks/:id/deliveries` | Recent delivery history. |
| POST   | `/api/v1/tenant/webhooks/:id/replay-dlq` | DLQ batch replay. |
| POST   | `/api/v1/tenant/webhooks/deliveries/:delivery_id/replay` | Single-delivery replay. |
