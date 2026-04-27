# Forms Builder

> Pages, sections, field types, routing, themes, attachments.

# Forms Builder

The builder lives at `/admin/forms`. Every action the UI offers
maps one-to-one onto the server contract — there's no "save, then
hope" step. Here's the feature surface.

## Field types

| Type | Notes |
|---|---|
| `text`, `textarea` | `max_length` honored client + server |
| `number` | `min` / `max` enforced; optional step |
| `email` | RFC 5322 light validation |
| `date` | ISO-8601 strings |
| `checkbox` | Boolean |
| `select`, `radio` | `options: string[]` |
| `multi_select` | Array of enum values |
| `file` | Up to 20 MiB, scanned server-side (see below) |
| `heading`, `section`, `static` | Layout only — not validated |

> **Annotation-driven prefill**
>
> When a form field was imported from an entity via the "From entity"
>   palette, the builder stamps <code>source.*</code> provenance. If
>   the entity has annotations, the importer pre-fills
>   label, required, max_length, min, max, and synthesizes a{' '}
>   <code>select</code> type from enum options.

> **Note**
>
> ">
>   As of 2026-04-26, source-stamped fields in the admin live preview
>   render through the LCAP <code>&lt;SmartField /&gt;</code> resolver
>   when the form/annotation type pair is on a strict allow-list
>   (<code>text↔string</code>, <code>textarea↔longtext</code>,{' '}
>   <code>number↔number</code>, <code>checkbox↔boolean</code>,{' '}
>   <code>date↔timestamp</code>, <code>select↔enum</code>,{' '}
>   <code>file↔file_ref</code>). Live edits to the entity's annotation
>   (label, formatting, ui_config) flow through to the preview
>   without rebuilding the form. Mismatches and missing annotations
>   fall back to the legacy renderer with a one-time{' '}
>   <code>console.warn</code>. Layout fields (<code>heading</code>,{' '}
>   <code>section</code>, <code>static</code>), <code>email</code>,{' '}
>   <code>radio</code>, and <code>multi_select</code> always stay
>   legacy. The public renderer at the public form URL also stays
>   legacy until a public-annotation read endpoint ships.

## Pages + sections

The builder's left rail shows pages; each page has one or more
sections; each section owns an ordered list of field keys. Moving a
field between pages is a one-click drag. Single-page forms render
without a stepper — the public renderer short-circuits when
`pages.length === 1`.

## Conditional routing

Open a page's **Routing** panel to add rules:

- **Simple mode** — `{field} {op} {value} → goto page`. Covers
  most cases.
- **Advanced mode** — raw JSONLogic for complex conditions. The
  save path validates every rule: no dangling `goto_page_id`, no
  unknown field refs, no cycles, no unreachable pages.

`goto_page_id: "__end__"` short-circuits to the submit state — use
it for branches that skip the remaining pages.

## File attachments

Drag in a **File upload** field and configure:

- `accepted_mime` (e.g. `image/*, application/pdf`)
- `max_size` in MiB (capped at the server's 20 MiB hard limit)
- `multiple` — whether the field accepts an array

The public renderer uploads files *before* the submit POST. Each
upload lands in `form_submission_attachments` with
`scan_status = 'pending'`. The three-layer scanner (MIME magic → VT
hash → LLM content) runs asynchronously; the renderer polls until
`scan_status = 'clean'` or displays the rejection reason. The
submit handler then claims all referenced attachments atomically
in one transaction with the submission INSERT.

> **Pending uploads block submit**
>
> The frontend refuses to POST the submission while any attachment
>   is still scanning. The backend's claim predicate (<code>
>   scan_status='clean' AND submission_id IS NULL</code>) would 422
>   it anyway — failing client-side gives users a clearer error.

## Themes + branding

The **Theme** panel controls form-level appearance:

- Pick any tenant theme (built-in or custom) or leave blank to use
  the tenant's active brand.
- Upload a logo + hero image (sanitized via the entity-file
  scanner) — both are served at `/api/v1/public/form-assets/:id`
  with 1h cache.
- Title / subtitle overrides render in the form header.

> **Custom CSS (enterprise)**
>
> Tenants with the <code>forms_custom_css</code> feature flag on
>   get a textarea to author per-form CSS. The server runs a
>   hand-rolled sanitizer on save (strips at-rules, banned
>   properties, and dangerous URL schemes) and scopes every rule to{' '}
>   <code>.form-public-shell[data-form-id="..."]</code> so one
>   form's CSS can never leak into another.

## Publishing

A form in `draft` status is visible in the admin but has no public
endpoints. Clicking **Publish** flips the status and creates an
immutable version row. You can always edit the draft, but the
public invite tokens continue to serve the last published version
until you re-publish.
