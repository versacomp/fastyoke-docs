# Vanity PDF filenames

> Generate human-readable filenames for submission PDFs using a pattern of slugs, submission IDs, field values, and dates.

# Vanity PDF filenames

By default, every submission PDF is named using the internal format
`form-submission-<id_short>-v<version>.pdf`. Vanity filename patterns let
you replace that with something meaningful — for example:
`invoice-request-2026-05-15-abc12345.pdf`.

Patterns are available on **Pro and above** and apply to all three PDF
render modes (Typst, AcroForm, and Overlay). The filename is resolved
at submit time and stored with the submission; regenerating a PDF reuses
the original filename verbatim.

## Where to configure

There are two places to set a pattern:

1. **Tenant default** — `Settings → Filename Defaults` sets a pattern
   that applies to every form that has no per-form override. You can
   also define tenant variables here (see [tenant variables](#tenant-variables)).

2. **Per-form override** — open the form in the builder, scroll to
   **PDF Designer → Filename**, and enter a pattern. A non-empty value
   takes precedence over the tenant default.

Both surfaces also accept the pattern via the REST API:

- `PUT /api/v1/settings/filename-defaults` — tenant default
- `POST /api/v1/forms` / `PATCH /api/v1/forms/:id` — `filename_pattern` field on the form definition

## Token reference

A pattern is a mix of literal text and `{token}` placeholders.

### Built-in tokens

| Token | Resolves to | Example output |
|---|---|---|
| `{form_name}` | Form name, slugified | `invoice-request` |
| `{form_version}` | Integer schema version | `3` |
| `{submission_id}` | Full UUID of the submission | `abc12345-de67-89f0-…` |
| `{submission_id_short}` | First UUID segment (8 chars) | `abc12345` |
| `{date}` | Submit date (UTC), `YYYY-MM-DD` | `2026-05-15` |
| `{datetime}` | Submit timestamp (UTC), `YYYYMMdd-HHmmss` | `20260515-142307` |
| `{counter}` | Internal row counter for this submission | `42` |

### Field value tokens

`{field:<key>}` inserts the submitter's value for a specific form field,
slugified. Replace `<key>` with the field's key as defined in the form
schema.

```
{form_name}-{field:invoice_number}-{date}
→ invoice-request-inv-1042-2026-05-15.pdf
```

Restrictions enforced at save time:

- The field key must exist in the form's current schema (`unknown_field`
  error if not).
- Fields marked as PII cannot be used in a filename
  (`pii_field_blocked`). If you need an identifier that appears in
  filenames, keep it as a non-PII reference field.

If the field has no value for a particular submission (empty or null),
the token resolves to an empty string and adjacent separators are
collapsed automatically — no doubled dashes.

### Tenant variables

`{var:<key>}` inserts a static value from your tenant variable map.
Use this for fixed labels that should appear in every filename across
all forms — for example a region code or customer prefix — without
coupling the value into every pattern string.

Define variables at `Settings → Filename Defaults → Variables`. Up to
**20** variables per tenant; keys must match `^[A-Za-z0-9_]+$` (max 64
chars), values are plain strings (max 128 chars).

```
{var:region}-{form_name}-{date}
→ us-east-invoice-request-2026-05-15.pdf
```

The variable key must exist in the tenant variable map when the pattern
is saved (`unknown_variable` error otherwise). Adding a variable later
does not retroactively validate saved patterns — update those patterns
after adding the variable.

## Sanitization rules

Each resolved token value is slugified before insertion:

1. Common Latin diacritics are ASCII-folded (`é → e`, `ü → u`, `ñ → n`, etc.).
2. Everything is lowercased.
3. Spaces collapse to a single `-`.
4. Characters outside `[a-z0-9._-]` are stripped.
5. Leading and trailing `-` / `_` are trimmed.
6. Adjacent repeated separators (`--`, `__`) are collapsed to one.

Literal text between tokens is **not** slugified. It must already
consist of safe characters — the validator rejects any literal that
contains characters outside `[A-Za-z0-9._-]` (`unsafe_chars` error).
This means you cannot use `/`, spaces, or `@` in literal text; use
hyphens as separators.

The resolved filename is capped at **200 characters** before the `.pdf`
suffix is appended. The pattern itself is limited to **256 characters**.

## Fallback

If the resolved filename is empty after all tokens are substituted
(for example, every token returned an empty value), the system falls
back to the legacy default:

```
form-submission-<submission_id_short>-v<version>.pdf
```

## Validation error codes

The API returns `422` with a stable `error.code` when a pattern fails
validation:

| Code | Cause |
|---|---|
| `unknown_token` | `{token}` is not a recognized token name or namespace |
| `unknown_field` | `{field:<key>}` references a field key not in the form schema |
| `pii_field_blocked` | `{field:<key>}` references a PII-flagged field |
| `unknown_variable` | `{var:<key>}` references a key not in the tenant variable map |
| `unsafe_chars` | Literal text contains a character outside `[A-Za-z0-9._-]` |
| `pattern_too_long` | Pattern exceeds 256 characters |

Variable map errors (returned by `PUT /api/v1/settings/filename-defaults`):

| Code | Cause |
|---|---|
| `too_many_variables` | More than 20 variables in the map |
| `variable_key_too_long` | Key exceeds 64 characters or contains invalid characters |
| `variable_value_too_long` | Value exceeds 128 characters |

Attempting to configure a pattern on a Hobby-tier tenant returns `402`
with `error.code = "tier_required"`.

## Examples

```
# Minimal — just the form name and submit date
{form_name}-{date}
→ invoice-request-2026-05-15.pdf

# Include a field value (non-PII reference number)
{form_name}-{field:order_ref}-{date}
→ delivery-notice-po-4021-2026-05-15.pdf

# Use a tenant variable for a fixed region label
{var:region}-{form_name}-{submission_id_short}
→ emea-intake-form-abc12345.pdf

# Full precision timestamp for audit trails
{form_name}-{datetime}
→ compliance-checklist-20260515-142307.pdf
```

> **Regenerated PDFs keep the original filename**
>
> When you regenerate a PDF from the admin panel, the regenerated copy
>   inherits the filename that was resolved at the time of the original
>   submission. Re-running the resolver with updated tenant variables or
>   a new pattern does not rename existing submissions.
