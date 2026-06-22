---
title: Lab M11 — Solution
summary: Answer key for the M11 webhooks lab — exact curls, a Node HMAC verifier, the delivery-id dedup pattern, and notes on rotate-secret and the SSRF guard.
order: 23
---

# Lab M11 — Solution

> **Reveal warning.** This page is the **answer key** for
> [Lab M11 — Webhooks](/docs/training/labs/M11-webhooks).
> Try each step on your own first; the friction is where the
> learning lives.

## Step 1 — Subscribe

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-receiver.example",
    "events": ["job.transition"],
    "description": "M11 lab"
  }' \
  https://www.fastyoke.io/api/v1/tenant/webhooks
```

Response:

```json
{
  "id": "wh_abc123",
  "url": "https://your-receiver.example",
  "events": ["job.transition"],
  "signing_secret": "whsec_...",
  "status": "active",
  "created_at": "2026-06-22T14:00:00Z"
}
```

The `signing_secret` is shown **only once** — capture it
into `FY_SECRET` immediately.

## Step 3 — Verify the HMAC (Node)

```js
import crypto from "node:crypto";

export function verify(rawBody, headerSig, secret) {
  const expected =
    "sha256=" +
    crypto.createHmac("sha256", secret).update(rawBody).digest("hex");
  // Always use timingSafeEqual to defeat trivial timing attacks.
  return crypto.timingSafeEqual(
    Buffer.from(expected),
    Buffer.from(headerSig),
  );
}
```

The `rawBody` must be the **exact bytes** the platform sent
— if your framework re-serializes the JSON, the
`X-FastYoke-Signature` hash won't match.

## Step 5 — Dedup by delivery id

```js
const seen = new Set();

app.post("/webhook", (req, res) => {
  const id = req.header("X-FastYoke-Delivery");
  if (seen.has(id)) return res.status(200).send("dup"); // no-op
  seen.add(id);
  // process …
  res.status(200).send("ok");
});
```

In production, replace the in-memory `Set` with a TTL'd
cache (Redis with an expiry of a few hours is the common
shape).

## Two operational notes

- **Rotate-secret dual window.** When you call
  `POST /webhooks/:id/rotate-secret`, both the previous
  and new secrets verify during a configurable grace
  window. Roll your receiver to the new secret before the
  window closes.
- **SSRF guard.** The platform refuses to dispatch to
  non-HTTPS targets, RFC-1918 ranges, link-local, and IPv6
  ULA. Refusals are counted separately and do **not** push
  a subscription toward the 10-consecutive-failure
  auto-pause threshold.

## Back to the lab

Return to [Lab M11 — Webhooks](/docs/training/labs/M11-webhooks).
