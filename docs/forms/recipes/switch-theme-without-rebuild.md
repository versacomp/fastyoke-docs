---
title: Switch a form's theme without rebuilding
summary: Theme change is a single save — no need to re-author the form.
order: 8
recipe: true
---

# Switch a form's theme without rebuilding

**When you'd use this — you're rebranding mid-quarter and need every live form to follow, or you want to A/B-test two themes on the same form without forking it.**

A form's theme is a pointer, not a copy. The fields, validation, and submission wiring all live on the form definition; the theme lives on a separate row that the public renderer joins in at request time. Swapping themes is a single save and never touches the form's structure.

## 1. Pick a theme from the Theme panel

Open the form in the admin shell and switch to the **Theme** panel. The dropdown lists every theme your tenant has authored (plus the FastYoke defaults). Pick one and click **Save**. The save is atomic — there is no intermediate "draft" state and no need to republish the form.

If you're A/B-testing, duplicate the form first, point the duplicate at the alternate theme, and split traffic at the invite/embed layer. The form definition stays identical between the two; only the theme pointer differs.

## 2. The public URL picks up the new theme on next page load

Form pages are not edge-cached by visitor — the theme join happens on every request — so a visitor who refreshes immediately after the save will see the new look. Embedded forms (the `<script>`-tag SDK) pick up the change on the next iframe mount, which in practice means the next page load on the host site.

The submitted payloads, validation rules, and downstream FSM jobs are unaffected by a theme swap. A submission collected under the old theme and one collected under the new theme are indistinguishable on the server; only the rendered chrome changes.

## See also

- [Style and brand your form](/docs/forms/tutorials/101-style-and-brand)
