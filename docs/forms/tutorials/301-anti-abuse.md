---
title: "Anti-abuse tuning"
summary: "Turnstile, honeypot, daily cap — what each catches and how to reset when you're locked out in dev."
order: 12
tier: "301"
---

# Anti-abuse tuning

::callout{type="info" title="What you'll build"}
`Patient Intake` gets the full three-layer abuse shield turned on:
Cloudflare Turnstile in front, a honeypot field behind it, and a
hard ceiling of `200` submissions per UTC day. You'll publish, then
prove each layer by tripping it on purpose.
::

::callout{type="note" title="Prerequisites"}
Finish [File uploads and scanning](/docs/forms/tutorials/301-file-uploads-and-scanning).
You need a published `Patient Intake` form with the insurance-page
file fields wired up.
::

The three layers stack: Turnstile rejects automation before the
request body is even parsed, the honeypot catches dumb form-fillers
that breeze past Turnstile by filling every visible input, and the
daily cap puts a ceiling on whatever still gets through. None of the
three replaces the others — they catch different shapes of abuse.

## 1. Open the Anti-abuse panel

In the Forms builder, open `Patient Intake` and switch to the
**Anti-abuse** panel in the form settings drawer. The panel holds
three toggles and one numeric input — Turnstile, honeypot, and the
daily cap. The defaults are conservative; this tutorial walks the
dial up on all three.

## 2. Enable Turnstile

Flip the **Turnstile** toggle on. The widget renders below the last
visible field on the public form and runs invisibly for most
visitors; suspicious traffic gets a managed challenge. The submitted
token rides on the submit POST and the server verifies it against
Cloudflare's API before any other validation runs.

::callout{type="warn" title="Tenant site key required"}
Turnstile needs a tenant-level Cloudflare site key configured under
**Settings → Integrations**. Without it the toggle saves but the
widget can't render, and submissions return `400 Bad Request` with
error code `challenge_failed` because the token is missing. Configure
the key once per tenant, not per form.
::

## 3. Enable the honeypot

Flip the **Honeypot** toggle on. The renderer injects an extra
field — wire name `forms_turnstile`, despite the misleading name —
that is hidden from real users via CSS but present in the DOM. Most
naive bots scrape the DOM and fill every input they find; a
submission with `forms_turnstile` non-empty is rejected with
`400 Bad Request` and error code `validation_failed`. No payload data
is stored on a rejection.

The honeypot catches a different population from Turnstile —
specifically, automation that solves or bypasses the Cloudflare
challenge but doesn't bother rendering the page. The two layers are
cheap and overlap on purpose.

## 4. Set the daily cap

In the **Daily submission cap** input, type `200`. The cap is a
per-tenant ceiling that resets at 00:00 UTC. Submissions beyond it
return `429` with error code `tier_quota_exceeded` until the next
day rolls over. Pick a number above your real expected volume but
low enough that a runaway script gets stopped before it fills the
attachments disk.

## 5. Save and re-publish

Click **Save**, then **Publish**. The published version row freezes
the anti-abuse config the same way it froze field config and the
routing graph in earlier tutorials. The public renderer at the
invite URL picks up the new shields on the next request.

::callout{type="tip" title="Locked out in dev?"}
The cap counters are per-tenant, not per-form, so a stress test
against one form burns through the day's budget for all of them. In
the admin Forms list, open the form's overflow menu and click
**Anti-abuse → Reset counters**. That clears the per-tenant rate
and daily-cap counters immediately. The same reset is documented
under [Reset Turnstile in dev](/docs/forms/recipes/reset-turnstile-in-dev).
::

```text
POST submit
   │
   ▼
┌──────────┐ fail  ┌────────────────────┐
│ Turnstile│ ────▶ │ 400 / challenge_fail
└────┬─────┘       └────────────────────┘
     │ pass
     ▼
┌──────────┐ filled ┌────────────────────┐
│ Honeypot │ ─────▶ │ 400 / validation_fail
└────┬─────┘        └────────────────────┘
     │ empty
     ▼
┌──────────┐ over   ┌──────────────────┐
│ Day cap  │ ─────▶ │ 429 / tier_quota
└────┬─────┘        └──────────────────┘
     │ under
     ▼
submission INSERT
```

Read the diagram top to bottom: each layer either short-circuits the
request with a specific outcome or hands off to the next. Nothing
writes a row until all three pass. That ordering matters for the
verification steps below — tripping the honeypot still consumes
Turnstile's verify call but does not touch the cap.

## Verify it worked

Run the three tests in order. Reset counters between them if you
exhaust the cap.

- **Turnstile missing.** Open the public invite URL in a browser
  with JavaScript disabled (or use curl with no `cf-turnstile-response`
  field) and POST a submission. The response is `400 Bad Request` with
  error code `challenge_failed`. No row lands in `form_submissions`.
- **Honeypot filled.** Open the invite URL with JS on, then use
  devtools to find the hidden `forms_turnstile` input and type
  anything into it before submitting. The response is `400 Bad Request`
  with error code `validation_failed`. No row lands in `form_submissions`
  and no attachments are claimed.
- **Cap exceeded.** In the **Anti-abuse** panel temporarily lower
  the daily cap from `200` to `1`, save, and re-publish. Submit
  once — it succeeds. Submit a second time — the response is `429`
  with error code `tier_quota_exceeded`. Click **Reset counters**
  to clear the lockout, then restore the cap to `200` and
  re-publish.

If all three tripwires fire as described, the shield is live.

## Next

Continue with [Custom CSS for branded forms](/docs/forms/tutorials/301-custom-css).
