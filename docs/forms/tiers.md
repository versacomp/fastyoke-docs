---
title: Forms tier entitlements
summary: What each tier unlocks for the Forms surface — active form count, daily submission ceiling, anti-abuse posture, PDF rendering, badge visibility.
order: 5
---

# Forms tier entitlements

Forms ship across all tiers. The differences are in scale and
configuration depth, not feature gates.

| Capability                            | Solo           | Pro              | Team             | Enterprise       |
| ------------------------------------- | --------------- | ---------------- | ---------------- | ---------------- |
| Active (published) forms              | 3               | Unlimited        | Unlimited        | Unlimited        |
| Daily submissions (aggregate)         | 100             | 1,000            | Unlimited        | Unlimited        |
| Turnstile challenge                   | Always-on       | Not used         | Not used         | Not used         |
| Daily cap / form (see [anti-abuse](/docs/forms/anti-abuse)) | 100 | 1,000 | Unlimited | Unlimited |
| Submission PDF — Typst (no template)  | ✓               | ✓                | ✓                | ✓                |
| Submission PDF — AcroForm / Overlay   | —               | —                | ✓                | ✓                |
| Vanity PDF filenames                  | —               | ✓                | ✓                | ✓                |
| Powered-by FastYoke badge             | Always-on       | Opt-in           | Opt-in           | Opt-in           |
| Embedded forms — inline mode          | ✓               | ✓                | ✓                | ✓                |
| Embedded forms — modal / slide / bubble | —             | ✓                | ✓                | ✓                |
| Form invites (Mint)                   | ✓ (capped)      | ✓                | ✓                | ✓                |
| HTTP ingestion API                    | —               | ✓                | ✓                | ✓                |

::callout{type="note" title="Form invite caps on Solo"}
Solo invite tokens have a 30-day maximum lifetime and a 500-submission
cap per token. Pro and above support 1-year tokens with no submission
limit.
::

::callout{type="tip" title="Crossing your daily ceiling"}
Hitting a daily submission ceiling raises a `429 Too Many Requests`
with error code `tier_quota_exceeded`. The ceiling resets at 00:00 UTC.
Submissions already accepted are never dropped. A one-time notification
email goes to the tenant admin when the cap is first hit in a UTC day.
::

## What an upgrade unlocks instantly

Tier changes propagate within ~30 seconds. After upgrading:

- New daily ceilings apply immediately.
- The Powered-by badge becomes optional (Pro+) — toggle it off from
  **Branding** settings.
- Vanity PDF filenames become available — set a `filename_pattern` on
  any form definition via the API or form settings.
- AcroForm and Overlay PDF modes unlock at Team+ for custom-template
  submissions.
- Embedded modal, slide, and bubble trigger modes unlock at Pro+.
- The HTTP ingestion API (`POST /api/v1/tenant/forms/:form_id/submissions`)
  becomes available at Pro+.
