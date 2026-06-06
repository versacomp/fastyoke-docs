---
title: "Style and brand your form"
summary: "Apply a theme, upload a logo and hero, customize the header and submit-button copy."
order: 3
tier: "101"
---

# Style and brand your form

::callout{type="info" title="What you'll build"}
A branded public `Patient Intake` form with your clinic logo, a hero
banner across the top, a custom header title, and submit-button copy
that reads like the rest of your intake flow.
::

::callout{type="note" title="Prerequisites"}
Finish [Build your first form](/docs/forms/tutorials/101-build-first-form)
before starting this tutorial. You need the `patient-intake` form
already saved and published.
::

The builder ships with neutral defaults so a brand-new form is
usable on day one. Once you have submissions flowing, swap in your
own theme and assets so the public URL looks like part of your
clinic — not part of FastYoke.

## 1. Open the Theme panel

From the admin shell, open **Forms** and click into `Patient Intake`.
In the form's right rail, open the **Theme** panel. This is where
every appearance setting on the form lives — theme picker, asset
uploads, and the header/button copy overrides.

The panel writes to the form record on save. Nothing here changes
the schema or the field keys, so styling edits are safe to make
against a published form without re-publishing.

## 2. Pick the Clinic Cool theme

In the theme picker, choose `Clinic Cool` from the dropdown.

Themes are tenant-scoped and live under **Settings → Themes**. If
you don't see `Clinic Cool` in the list, an admin needs to create
it there first — give the theme that exact name and save it, then
come back to the form and re-open the picker.

Leaving the theme picker blank is also valid. A blank picker tells
the renderer to fall back to the tenant's active brand, which is
the brand configured under **Settings → Branding**. Pick a theme
explicitly when you want this form to look different from the rest
of the tenant's surfaces.

## 3. Upload the logo and hero

Below the theme picker, the panel has two upload slots: **Logo**
and **Hero image**.

1. Drop `logo.png` into the logo slot. Aim for a transparent PNG
   sized around 240×80 — the renderer scales it down to fit the
   header.
2. Drop `hero.jpg` into the hero slot. The hero is rendered full-
   width above the form title, so a wide landscape crop reads best.

Both uploads run through the entity-file scanner before they're
accepted. That's the same pipeline file fields use — MIME magic
check, size limit, and an antivirus pass. A rejected file shows
its rejection reason inline next to the slot; fix the file and
re-drop it.

Accepted assets are stored on the form record and served from
`/api/v1/public/form-assets/:id` with a one-hour cache, so updates
take effect for new visitors within the hour.

## 4. Customize the header and button copy

Two text inputs live below the asset slots:

- **Title override.** Set this to:
  `Welcome to Bayview Clinic — please complete your intake.`
- **Submit-button copy.** Set this to: `Finish intake.`

The title override replaces the default form name in the public
header. The submit-button copy replaces the renderer's default
**Submit** label on the last page only — the **Next** buttons on
earlier pages are unaffected.

Both fields accept plain text. Punctuation, em-dashes, and trailing
periods all render verbatim, so write the copy exactly the way you
want it to read.

## 5. Save

Click **Save** at the top of the builder. The theme, the two
assets, and the two copy overrides all persist in the same write.
You don't need to re-publish — appearance edits flow to the
published version on save.

## Verify it worked

Copy the public URL from the form's detail header and open it in
an incognito window. You should see:

- The `Clinic Cool` theme applied to the page background, form
  card, and button colors.
- `logo.png` in the header, scaled to fit.
- `hero.jpg` rendered full-width above the title.
- The title reading `Welcome to Bayview Clinic — please complete
  your intake.`
- The button on the final page reading `Finish intake.` instead of
  the default **Submit**.

If any of the assets are missing, re-open the **Theme** panel and
check whether the scanner rejected the upload — a rejected file
leaves the slot empty but keeps the rejection reason visible.

## Next

Continue with [Share with an invite or embed](/docs/forms/tutorials/101-share-invite-and-embed).
