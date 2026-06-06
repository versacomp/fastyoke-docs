---
title: "Vanity PDF filenames"
summary: "Replace the default `submission-<id>.pdf` with a readable per-submission filename."
order: 15
tier: "301"
---

# Vanity PDF filenames

::callout{type="info" title="What you'll build"}
Every `Patient Intake` submission's `pdf_download_url` ends in a
readable, scenario-specific filename like
`patient-intake-nguyen-2026-05-15.pdf` instead of the opaque default
`form-submission-<id>-v1.pdf`. The pattern resolves at submit time
and sticks with the submission forever — regenerating the PDF reuses
the same filename.
::

::callout{type="note" title="Prerequisites"}
Finish [Server-to-server submission](/docs/forms/tutorials/301-http-ingestion).
You need a published `Patient Intake` form with PDF rendering already
turned on, and at least one submission so you can compare the
`pdf_download_url` before and after.
::

The pattern is a small template string of literal text and `{token}`
placeholders. The resolver substitutes each token, slugifies the
result, caps it at 200 characters, and appends `.pdf`. Patterns are
available on Pro and above.

## 1. Open the per-form override

Open `Patient Intake` in the builder and scroll to **PDF Designer →
Filename**. This is the per-form override — a non-empty value here
takes precedence over the tenant-wide default at **Settings →
Filename Defaults**. Leave the tenant default alone for this tutorial
so you can see the override take effect on just this form.

## 2. Set the pattern

Enter:

```
{form_name}-{field:last_name}-{date}
```

Three tokens, two literal hyphens. At submit time:

- `{form_name}` resolves to the form's slugified name —
  `patient-intake`.
- `{field:last_name}` resolves to the submitter's `last_name`
  field value, slugified. The field key must exist in the
  published schema; if you marked `last_name` as PII, the
  validator rejects the pattern with `pii_field_blocked` —
  swap to a non-PII reference field (an MRN, a visit number)
  or unflag `last_name` if it's safe to expose in filenames.
- `{date}` resolves to the submit date in UTC as
  `YYYY-MM-DD`.

Diacritics fold to ASCII, spaces collapse to `-`, and adjacent
separators de-dup automatically. If a token resolves to an empty
string (an optional field left blank) the adjacent hyphen is
collapsed rather than left dangling.

## 3. Save and re-publish

Save the form. The pattern is validated synchronously — an unknown
token name returns `unknown_token`, an unknown field key returns
`unknown_field`, and literal text outside `[A-Za-z0-9._-]` returns
`unsafe_chars`. Fix any errors inline, then re-publish so new
submissions pick up the override. Older submissions keep whatever
filename they resolved to at the time — the resolver never renames.

::callout{type="info" title="Available variables"}
The built-in tokens are `{form_name}`, `{form_version}`,
`{submission_id}`, `{submission_id_short}`, `{date}`,
`{datetime}`, and `{counter}`. Field-value tokens use
`{field:<key>}` where `<key>` is a non-PII field key in the
current schema. Tenant variables use `{var:<key>}` where
`<key>` is defined at **Settings → Filename Defaults →
Variables**. Full reference: [Vanity PDF filenames](/docs/forms/pdf-vanity-filenames).
::

## Verify it worked

Submit `Patient Intake` twice — once with `last_name = Nguyen`,
once with `last_name = O'Brien` — using the ingest curl from the
previous tutorial or the public renderer. Then:

- Open each submission's detail panel and copy the
  `pdf_download_url`. The first URL should end in
  `patient-intake-nguyen-<today>.pdf`; the second in
  `patient-intake-obrien-<today>.pdf` (the apostrophe is
  stripped by the slugifier).
- Download both PDFs and confirm the filename the browser saves
  matches the URL suffix — the server sets `Content-Disposition`
  from the same resolved name.
- Regenerate one of the PDFs from the admin panel. The
  regenerated copy keeps the original filename; the resolver
  does not re-run with the current pattern.

If both URLs carry the templated filename and the regenerated PDF
matches the original, the override is wired correctly.

## Next

This is the last tutorial in the guided arc. From here:

- [Recipes index](/docs/forms/recipes) — short, copy-pasteable
  patterns for specific problems (conditional routing, file
  uploads, webhook fan-out).
- [Forms reference](/docs/forms/builder) — the full builder
  reference: every field type, every validator, every PDF
  renderer mode, and the public-submit and ingest API
  contracts in detail.
