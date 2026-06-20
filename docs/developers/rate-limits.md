---
title: Rate limits
summary: What the platform throttles, the 429 response shape, the Retry-After header, and recommended client backoff behaviour.
order: 5
---

# Rate limits

The platform meters and bills on transitions, storage, egress,
and PDFs (see Pricing). Separately, the platform **rate-limits**
a few request types to prevent abuse. This page documents the
limits, the 429 response, and the client behaviour the platform
expects.

## What's limited

- **Anonymous form submissions** — public form endpoints that
  accept unauthenticated submissions are rate-limited per-IP per
  tenant. The limit defends against scrapers and accidental
  client loops.
- **Public verify endpoints** — the E-signatures public verify
  endpoint and any other unauthenticated verification path.
  Rate-limited per-IP.
- **A per-tenant default ceiling** — every API endpoint carries a
  per-tenant ceiling on requests-per-second to protect the
  platform from runaway clients.

Rate limits are not metering. Metering charges you (see Pricing);
rate limits **refuse** you. A 429 response is a refusal, not a
charge.

## The 429 response

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 15
Content-Type: application/json

{
  "error": "rate_limited",
  "message": "too many requests"
}
```

The `error` field is always `rate_limited` for these responses.
The `Retry-After` header indicates the number of seconds to wait
before the next attempt. The platform may include additional
detail in `details` (e.g., the limit category that was hit) but
clients should not depend on that for retry logic.

## Recommended client behaviour

- **Respect `Retry-After`.** Don't retry sooner than the header
  indicates. The platform may extend the window on continued
  abuse.
- **Exponential backoff with jitter** for any retry beyond what
  `Retry-After` covers. A common shape: `min(60, 2^attempt) +
  random(0, 1)` seconds.
- **Surface the user-facing impact** in your client UI instead of
  silently swallowing the 429. A form submitter who's rate-limited
  should see "please wait a moment" rather than a generic
  "something went wrong."

## Metering vs. rate-limiting

These are different mechanisms:

| | Metering | Rate-limiting |
|---|---|---|
| Purpose | Bill you for usage | Refuse abusive traffic |
| Response | 2xx + the operation runs | 429 + the operation is rejected |
| Configuration | Per-tier on Pricing | Platform default + per-endpoint overrides |
| Documentation | Pricing | This page |

The Pricing page covers what's metered. This page covers what's
refused.
