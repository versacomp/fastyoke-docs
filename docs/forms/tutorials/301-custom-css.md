---
title: "Custom CSS"
summary: "Wire your brand's exact hex tokens with per-form CSS — Enterprise / ISV only."
order: 13
tier: "301"
---

# Custom CSS

::callout{type="info" title="What you'll build"}
`Patient Intake` picks up the clinic's exact brand palette —
`#0F5C7A` as the primary action color and `#E0F2F8` as the page
surface — without forking the public renderer or touching tenant-wide
theme tokens. The styling is per-form and ships with the published
version.
::

::callout{type="note" title="Prerequisites"}
Finish [Anti-abuse tuning](/docs/forms/tutorials/301-anti-abuse).
You need a published `Patient Intake` form with shields on.
::

::callout{type="warn" title="Enterprise / ISV tier"}
Custom CSS is gated by the `forms_custom_css` feature flag on the
tenant. Lower tiers see the theme tokens panel but not the **Custom
CSS** textarea. If the textarea is missing on an Enterprise / ISV tenant,
confirm the flag is set under **Settings → Feature flags**.
::

Tenant-wide theme tokens cover most branding needs in one shot, but
clinics, law firms, and agencies routinely have a specific
intake-form palette that differs from the rest of their tenant
shell. Per-form CSS is the escape hatch — narrow, sanitized, and
scoped so one form's rules can never bleed into another.

## 1. Open the Custom CSS textarea

Open `Patient Intake` in the Forms builder and switch to the
**Theme** panel in the settings drawer. The panel shows the usual
color, font, and spacing tokens at the top and a **Custom CSS**
textarea below them. The textarea is empty by default.

## 2. Paste the clinic palette

Paste the following block into the textarea. It declares two CSS
custom properties at `:root`, then wires them onto the submit button
and the page surface.

```css
:root {
  --form-primary: #0F5C7A;
  --form-surface: #E0F2F8;
}
button[type="submit"] { background: var(--form-primary); color: white; }
.form-page { background: var(--form-surface); }
```

Two custom properties keep the hex codes in one place — change them
once and every selector that reads `var(--form-primary)` updates. If
you have more buttons or accent surfaces to brand, extend the same
pattern rather than sprinkling hex codes through individual rules.

## 3. Save

Click **Save**. The server-side sanitizer runs synchronously on save
and either accepts the block whole or rejects it with `422` and the
offending rule echoed back in the response body. Fix the rule the
sanitizer named and save again. Sanitization is a save-time gate,
not a render-time one — once a block is saved it has already passed.

## 4. Confirm on the public form

Open the public invite URL for `Patient Intake` in a fresh browser
tab. The page background should render `#E0F2F8` and the
**Submit** button should render `#0F5C7A`. If the colors don't
land, check the **Theme** panel's tenant-wide tokens — a tenant
token with higher specificity can override a per-form rule, and the
fix is usually to bump the per-form selector's specificity rather
than to weaken the tenant token.

::callout{type="note" title="Sanitizer rules"}
The sanitizer enforces four invariants. At-rules like `@import` and
`@font-face` are stripped, so you cannot pull in remote stylesheets
or remote fonts. Banned properties — `position: fixed` is the
canonical example — are dropped from the declaration block they
appear in. URL schemes other than `https:` and `data:image/*` are
blocked, which kills `javascript:` and `http:` background images.
Every rule is auto-scoped to
`.form-public-shell[data-form-id="<id>"]` at save time, so one
form's CSS can never leak into another form, the admin shell, or
the tenant chrome. The scoping is transparent — you write
`button[type="submit"] { ... }` and the saved rule reads
`.form-public-shell[data-form-id="<id>"] button[type="submit"] { ... }`.
::

## Verify it worked

Run two checks in order.

- **Good CSS lands.** Open the public invite URL. Use devtools to
  confirm the **Submit** button computed `background-color` reads
  `rgb(15, 92, 122)` — that's `#0F5C7A` — and the page surface
  reads `rgb(224, 242, 248)` — that's `#E0F2F8`. Inspect any rule
  in the Styles pane and confirm the selector is prefixed with
  `.form-public-shell[data-form-id="<id>"]`.
- **Bad CSS is rejected.** Back in the **Theme** panel, paste
  `@import url(evil.css);` at the top of the textarea and click
  **Save**. The save fails with `422` and the response body names
  the offending rule. Remove the line and save again — the form
  returns to a saving-clean state.

If both checks pass, the per-form palette is live and the sanitizer
is doing its job.

## Next

Continue with [HTTP ingestion](/docs/forms/tutorials/301-http-ingestion).
