# Anti-abuse for public forms

> Turnstile challenges, honeypots, daily caps, and per-IP rate limits keep public form submissions clean without a CAPTCHA wall.

# Anti-abuse for public forms

Public forms are reachable by anyone with the invite URL. The
platform defends them with four overlapping layers:

1. **Cloudflare Turnstile** — invisible-first challenge (Hobby tier).
2. **Honeypot field** — bots that fill every input get auto-rejected.
3. **Daily submission cap** — per-tenant ceiling sized to your tier.
4. **Per-IP rate limit** — token-bucket throttle on a single client.

You don't configure any of this to get the defaults. The layers apply
automatically based on your tier.

## Turnstile

Hobby-tier public forms render a Cloudflare Turnstile widget below
the last visible field. The widget runs invisibly for most visitors;
suspicious traffic gets a managed challenge.

The submitted token is sent as the `cf_turnstile_response` field on
the form payload. The server verifies the token against the Turnstile
API before any other validation runs — a failed or missing verify
returns `400 Bad Request` with error code `challenge_failed`.

> **Pro and above**
>
> Pro, Team, and Enterprise forms do not require Turnstile. On those
>   tiers the widget is not rendered and the field is not expected.

## Honeypot

Every public form ships with a hidden `__hp_check__` companion field
that real users never see (it is rendered off-screen with
`aria-hidden="true"` and `tabindex="-1"`). Submissions with any
non-empty value for that field are rejected with `400 Bad Request` and
error code `validation_failed`.

The field name is consistent across all forms so that browser
autofill and bot-training datasets interact with it predictably.

## Timing check

Each public form GET response includes a signed `__render_ts__`
token. Submissions must echo this value back. The server rejects
submissions where the token was issued less than 1.5 seconds ago
(bot-speed) or more than 24 hours ago (stale page). Both cases return
`400` with error code `validation_failed`.

## Link-count filter

Text, textarea, and richtext fields are scanned for URL-like
substrings (`https://...`). Any field containing more than three URLs
causes the submission to be rejected with `400` and error code
`validation_failed`. This catches link-injection spam before the
payload reaches storage.

## Daily submission cap

Each tenant carries a ceiling on submissions per UTC day, sized by
tier:

| Tier        | Submissions / day |
| ----------- | ------------------ |
| Hobby       | 100                |
| Pro         | 1,000              |
| Team        | Unlimited          |
| Enterprise  | Unlimited          |

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
| Hobby       | 10 / minute  | 3               |
| Pro         | 60 / minute  | 10              |
| Team        | 600 / minute | 50              |
| Enterprise  | 600 / minute | 50              |

The bucket starts full (burst allowance), then refills at the refill
rate. Requests beyond the available tokens return `429 Too Many
Requests` with message `rate limit exceeded`.

The limit is intentionally generous at higher tiers — it exists to
catch obvious flood patterns, not to limit legitimate kiosks behind
NAT. If you run a kiosk on Hobby with a shared IP, upgrade to Pro or
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
