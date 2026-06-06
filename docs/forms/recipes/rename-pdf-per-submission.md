---
title: Rename the downloaded PDF per submission
summary: Set a vanity filename pattern once; every submission's download link uses it.
order: 5
recipe: true
---

# Rename the downloaded PDF per submission

**When you'd use this** — operators don't want to triage 200
`form-submission-abc12345-v3.pdf` files a day. A vanity pattern stamps
each PDF with a human-readable name at submit time, and regenerated
copies inherit the original.

## 1. Open the form's filename field

In the builder, open the form, scroll to **PDF Designer → Filename**,
and put the pattern there. A non-empty value at this surface overrides
whatever's set as the tenant default.

If you want every form in the tenant to use the same shape, set it once
at **Settings → Filename Defaults** instead — per-form patterns then
override on a form-by-form basis.

## 2. Enter a pattern

Patterns mix literal text with `{token}` placeholders. A reasonable
default for an order workflow:

```
{form_name}-{date}-{submission_id_short}
→ order-intake-2026-05-15-abc12345.pdf
```

Common tokens you'll reach for:

- `{form_name}` — the form's name, slugified.
- `{date}` — submit date in `YYYY-MM-DD`.
- `{submission_id_short}` — first 8 chars of the submission UUID, for
  uniqueness.
- `{field:<key>}` — a submitter-supplied value. The field must exist in
  the schema and must not be flagged PII, or the save call returns 422.

The resolver runs at submit time and stores the result with the
submission, so the download link uses the vanity name immediately. The
pattern is capped at 256 characters and the resolved filename at 200.

::callout{type="info" title="Pro tier and above"}
Vanity patterns require Pro or higher. Solo-tier tenants get a 402
  with `error.code = "tier_required"` when they try to save one.
::

## See also

- [Vanity PDF filenames tutorial](/docs/forms/tutorials/301-vanity-filenames)
- [PDF vanity filenames reference](/docs/forms/pdf-vanity-filenames)
