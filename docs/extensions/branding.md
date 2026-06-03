---
title: Branding & the Powered-by badge
summary: Where the FastYoke "Powered by" badge appears, and how Pro and above can opt out per surface.
order: 60
---

# Branding & the Powered-by badge

FastYoke surfaces a small **Powered by FastYoke** attribution badge on
public-facing pages and in generated documents. The badge is controlled
by a single toggle — the same `show_powered_by` flag — and the visibility
rule is the same everywhere:

| Tier              | Badge behaviour                                 |
| ----------------- | ----------------------------------------------- |
| Solo             | Always on. Cannot be disabled.                  |
| Pro / Team / Enterprise / Fleet | Off by default. Admin can opt in. |

---

## Where the badge appears

### PublicForm entry and success state

The badge renders as a 12-px gray footer link at the bottom of the form
page, both before the respondent fills in fields and after a successful
submission. A `MarketingPlug` card (a bordered CTA promoting FastYoke's
Solo plan) appears alongside it on the success state; both surfaces
respect the same flag, so opting out removes all FastYoke attribution
from the form flow in one action.

The badge links to `https://fastyoke.io/?ref=public-form`.

### PublicWorkspace (`/p/:slug`)

The badge appears as a footer on the public-facing workspace page.
Visibility is driven by the same `show_powered_by` field returned by
`GET /api/v1/public/branding`.

### Embedded forms (iframe / SDK)

When a form is loaded via an embed token (`?em=…`), badge visibility
comes from the embed-config endpoint rather than the public branding
endpoint. The server computes this from tier alone — Solo embeds always
show the badge; Pro and above do not, regardless of the `show_powered_by`
opt-in toggle. There is no per-embed override.

### Submission PDFs (Typst, Overlay, AcroForm)

All three PDF render paths append a small footer line on the last page.
The exact text depends on the badge state snapshotted at submit time:

| State at submit | Footer text on PDF |
| --------------- | -------------------|
| Badge on (Solo or opted-in Pro+) | `Submission <id> · Generated with FastYoke · fastyoke.io` |
| Badge off (Pro+ opted out) | `Submission <id>` (audit line only, no FastYoke mention) |
| Legacy row (created before this feature) | `Submission <id> · rendered by FastYoke` |

The snapshot is taken when the submission is first persisted and stored
in `form_submission_attachments.show_powered_by_snapshot`. Admin
regenerate-PDF reads the snapshot, not the live tenant setting, so the
footer is byte-stable across regenerates — a legal-fidelity guarantee.

### CMS render JSON (`GET /api/v1/cms/render`)

The CMS render response includes a top-level `show_powered_by: bool`
field for Cloudflare Worker / SDK consumers to use when injecting badge
markup into CMS-rendered pages. The value follows the same Solo/Pro+
rule as the other surfaces.

---

## Controlling the toggle

### Admin UI

Navigate to **Settings → Branding** (`/admin/settings/branding`) and
scroll to the **Powered-by badge** toggle. The toggle is labelled
**Show "Powered by FastYoke" badge**.

- **Solo tenants**: the toggle is read-only and locked to on. An
  upgrade hint appears below it.
- **Pro+ tenants**: the toggle is editable. Default is off (badge
  hidden). Flip it on to display the badge on all surfaces.

### API

`PUT /api/v1/settings/branding` — include `show_powered_by` in the
`config` object:

```json
{
  "config": {
    "show_powered_by": true
  }
}
```

Setting `show_powered_by: false` on a Solo tenant returns
**422 Unprocessable Entity** with the message:

```
Solo tier cannot opt out of the Powered-by badge — upgrade to Pro or higher to hide it
```

Setting `show_powered_by: true` on a Solo tenant is accepted (no-op —
the badge is always on for Solo regardless of the stored value).

---

## What is not available

There is no custom-branding replacement — no option to swap the FastYoke
badge for your own attribution mark. The badge is either shown or hidden.
Logo, colours, and other brand tokens in Branding Settings apply to the
admin shell only and do not replace the attribution badge on public surfaces.

---

## Related

- [Forms tier entitlements](/docs/forms/tiers) — tier table row for
  "Powered-by FastYoke badge".
- [Vanity PDF filenames](/docs/forms/pdf-vanity-filenames) — PDF render
  documentation including filename pattern tokens and tier gating.
- [Embed forms](/docs/forms/embed) — embed configuration and badge
  behaviour in iframe contexts.
