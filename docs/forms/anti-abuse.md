---
title: Anti-abuse for public forms
summary: Turnstile challenges, honeypots, daily caps, and per-IP rate limits keep public form submissions clean without a CAPTCHA wall.
order: 30
---

# Anti-abuse for public forms

Public forms are reachable by anyone with the invite URL. The
platform defends them with several overlapping layers:

1. **Cloudflare Turnstile** — invisible-first challenge (Solo tier).
2. **Bot heuristics** — hidden-field traps, render-time signing, and
   payload-content filters reject obvious automation server-side.
3. **Daily submission cap** — per-tenant ceiling sized to your tier.
4. **Per-IP rate limit** — token-bucket throttle on a single client.

You don't configure any of this to get the defaults. The layers apply
automatically based on your tier. Specific thresholds and field
names are intentionally undocumented — publishing them would let a
targeted bot bypass each check by name.

## Turnstile

Solo-tier public forms render a Cloudflare Turnstile widget below
the last visible field. The widget runs invisibly for most visitors;
suspicious traffic gets a managed challenge.

The submitted token is sent as the `cf_turnstile_response` field on
the form payload (a Cloudflare-defined name, required for HTTP
ingestion integrations). The server verifies the token against the
Turnstile API before any other validation runs — a failed or missing
verify returns `400 Bad Request` with error code `challenge_failed`.

::callout{type="tip" title="Pro and above"}
Pro, Team, and Enterprise / ISV forms do not require Turnstile. On those
tiers the widget is not rendered and the field is not expected.
::

## Bot heuristics

Beyond Turnstile, every public form runs a layered set of server-side
heuristics: a hidden-field trap that real browsers ignore, a signed
render-time token that detects pre-canned payloads and stale pages,
and a content filter that catches link-injection spam in text
fields. Submissions that fail any heuristic are rejected with
`400 Bad Request` and error code `validation_failed`. No payload data
is stored on a rejection.

The exact field names, signing windows, and content thresholds are
not published. Integrators submitting through the HTTP ingestion API
do not need them — submissions originating from a real browser
session on the rendered form page carry the required fields
automatically.

## Daily submission cap

Each tenant carries a ceiling on submissions per UTC day, sized by
tier:

| Tier        | Submissions / day |
| ----------- | ------------------ |
| Solo       | 100                |
| Pro         | 1,000              |
| Team        | Unlimited          |
| Enterprise / ISV | Unlimited          |

Exceeding the cap returns `429 Too Many Requests` with error code
`tier_quota_exceeded`. The cap resets at 00:00 UTC.

When a tenant's count first reaches the cap in a UTC day, the platform
sends a one-time email notification to the tenant's primary admin. The
email fires once per cap per day — there is no repeated alerting for
subsequent rejections within the same day.

## Per-IP rate limit

A token-bucket throttle limits bursts from a single client IP per
form slug. The limits are sized by tier:

| Tier        | Refill rate  | Burst allowance |
| ----------- | ------------ | --------------- |
| Solo       | 10 / minute  | 3               |
| Pro         | 60 / minute  | 10              |
| Team        | 600 / minute | 50              |
| Enterprise / ISV | 600 / minute | 50              |

The bucket starts full (burst allowance), then refills at the refill
rate. Requests beyond the available tokens return `429 Too Many
Requests` with message `rate limit exceeded`.

The limit is intentionally generous at higher tiers — it exists to
catch obvious flood patterns, not to limit legitimate kiosks behind
NAT. If you run a kiosk on Solo with a shared IP, upgrade to Pro or
contact support.

## What gets notified

The platform sends a debounced email to the tenant admin when:

- The daily submission cap is first crossed in a UTC day.
- The daily PDF render quota is first crossed in a UTC day (if PDF
  generation is enabled on the form).

Individual per-request rejections (honeypot, Turnstile, timing,
link-count, rate limit) are logged server-side but do not trigger
notifications. No form-payload data is stored on a rejection — the
body is discarded before any row is written.
