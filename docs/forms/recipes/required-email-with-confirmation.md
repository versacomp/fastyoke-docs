---
title: Required email with confirmation copy
summary: Force an email field, surface a clear "we'll email you" line so the submitter knows what's coming.
order: 2
recipe: true
---

# Required email with confirmation copy

**When you'd use this** — any form where the submitter expects a copy of
their answers (or a follow-up) by email, and the success screen should
say so plainly.

## 1. Add a required email field

In the builder, drag an **email** field onto the page where you collect
contact info — or onto page one for a single-page form. Set:

- `key` — `email`
- Label — `Email address`
- Required — on
- Helper text — `We'll email you a copy of your answers.`

The `email` field type runs RFC 5322 light validation client-side and
again on the server, so a malformed address never makes it into the
submission row. The helper text renders directly under the input on the
public form, which is the first place the submitter actually looks for
reassurance.

## 2. Match the post-submit screen to the promise

After the submitter clicks **Submit**, the public renderer shows a
thank-you screen. Edit the form's **Title** and **Subtitle** on the
**Theme** panel so the post-submit copy lines up with the helper text —
e.g. title `Thanks!`, subtitle `A copy of your answers is on its way to
your inbox.` Subtitle text renders inside the form header on both the
form and the confirmation state, so the reassurance survives the submit
hand-off without needing any new template syntax.

If you also want the operator to receive a copy, configure that
downstream in the form's notification routing — the public renderer's
success screen only governs what the submitter sees.

## See also

- [Build your first form](/docs/forms/tutorials/101-build-first-form)
- [Forms Builder reference](/docs/forms/builder)
