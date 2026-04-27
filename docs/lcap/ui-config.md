# UI config reference

> Every `@ui/*` key the resolver respects, organized by field_type. The annotation row's `ui_config_json` is the single source of truth.

# UI config reference

`ui_config_json` is a free-form JSON object on the annotation
row; its `@ui/*` keys drive the resolver. This page is the
exhaustive reference.

The keys are validated against the type's allow-list at write
time (PUT handler refuses unknown component slugs with a 422)
and at render time (the resolver console-warns and falls back
to defaults).

## Universal keys

These keys apply to every `field_type`.

| Key | Type | Effect |
|---|---|---|
| `@ui/component` | string | Override the default component for the type. Validated against the type's allow-list. |
| `@ui/visible_when` | string | Expression: when false, hide the field. **Team-tier gated.** Hobby/Pro tenants see the field rendered as if always-true. |
| `@ui/compute` | string | Expression: when set, the field is read-only and displays the computed value. **Team-tier gated.** |
| `@ui/validate_when` | string | Expression: when false, surface a zod-style validation error. **Team-tier gated.** |

Expression keys evaluate inside a sandboxed QuickJS-emscripten
runtime — no DOM access, no `fetch`, no `eval`. The available
context: `value`, `record`, `form`, `now`, `tenant_id`,
`user_role`. See [the SmartField resolver](/docs/sdk/reference)
for the full Border Control contract.

## `field_type = "string"`

| `@ui/component` | Effect |
|---|---|
| `text` *(default)* | Native `<input type="text">`. |
| `password` | Native `<input type="password">`. |
| `email` | Native `<input type="email">` with browser-side validation. |
| `url` | Native `<input type="url">`. |
| `phone` | Renders as `<input type="tel">`. |
| `slug` | Same render as text; reserved for future slug-validation refinement. |

### Input formatting (string)

Two optional keys reformat the user's input as they type. Both
work together — mask runs first, uppercase after, so literals
in the mask are unaffected by case folding.

| Key | Type | Effect |
|---|---|---|
| `@ui/input_mask` | string | Format the value as the user types. Cursor position is preserved across the format pass so the next typed char lands where the user expects. |
| `@ui/uppercase` | boolean | Uppercase typed letters. Stacks cleanly with `@ui/input_mask`. |

**Mask token vocabulary** (matches `text-mask` / `imask`
conventions):

| Token | Accepts |
|---|---|
| `0` or `9` | digit (`0–9`) |
| `a` or `A` | letter (`a–z`, `A–Z`; case-insensitive accept) |
| `*` | alphanumeric |
| anything else | literal — rendered verbatim |

**Common mask examples:**

| Mask | Reads as |
|---|---|
| `000-00-0000` | US Social Security Number |
| `(000) 000-0000` | US phone |
| `00000-0000` | US ZIP+4 |
| `aaa-000` | Canadian postal code (use with `@ui/uppercase: true`) |
| `aa00-00-aa` | UK passport-style mixed alphanumeric |

The same keys are honored on Forms v2 fields stamped with an
`entity_field` source (admin-preview SmartField routing). For
free-form Forms v2 fields without an entity binding, the same
behavior is available via `field.mask` / `field.uppercase` on
the form schema directly.

## `field_type = "longtext"`

| `@ui/component` | Effect |
|---|---|
| `textarea` *(default)* | Native `<textarea>`. |
| `richtext` | TipTap-based editor from `@fastyoke/lcap-richtext`. Stores HTML-escaped sanitized markup. |
| `markdown` | Markdown editor from `@fastyoke/lcap-markdowneditor`. Stores raw markdown. |
| `code` | Monaco-based editor from `@fastyoke/lcap-codeeditor`. Stores raw source. |

| Key | Type | Effect |
|---|---|---|
| `@ui/code_language` | string | Syntax highlighting target for `code` component (e.g. `"javascript"`, `"sql"`, `"yaml"`). |

## `field_type = "number"`

| `@ui/component` | Effect |
|---|---|
| `number` *(default)* | Native `<input type="number">`. |
| `currency` | `Intl.NumberFormat` with `style: "currency"`. Display only — storage stays a plain JSON number. |
| `percent` | `Intl.NumberFormat` with `style: "percent"`. Storage stays decimal (`0.07` displays `7%`). |
| `slider` | Reserved for a range-slider follow-on. Renders as `number` today. |
| `rating` | Reserved for a star-rating follow-on. Renders as `number` today. |

| Key | Type | Effect |
|---|---|---|
| `@ui/format` | string | Alias for `@ui/component` when the value happens to be `currency` / `percent` / `decimal`. Either spelling works. |
| `@ui/currency_code` | string | ISO-4217 (`USD`, `EUR`, `JPY`, …). Default: `USD`. Drives the locale-rendered prefix/suffix. |
| `@ui/decimal_places` | number | Clamps `minimumFractionDigits` + `maximumFractionDigits`. Also drives the zod refinement that rejects extra precision. |
| `@ui/use_separators` | boolean | Toggle `useGrouping`. Default: `true` (i.e. `1,234.56`). Set to `false` for `1234.56`. |
| `@ui/min` | number | UI bound (also surfaced in zod). |
| `@ui/max` | number | UI bound. |
| `@ui/step` | number | Spinner step + zod multiple-of check. |

## `field_type = "boolean"`

| `@ui/component` | Effect |
|---|---|
| `checkbox` *(default)* | Native checkbox. |
| `switch` | Same `<input type="checkbox">` with `role="switch"`. |
| `radio` | Yes/No radio pair. Requires `options_json` to declare the two values. |

## `field_type = "timestamp"`

| `@ui/component` | Effect |
|---|---|
| `date` *(default)* | Native `<input type="date">`. |
| `datetime` | Native `<input type="datetime-local">`. |
| `time` | Native `<input type="time">` (time-of-day only). |
| `daterange` | Reserved for a range picker; renders as `date` today. |

| Key | Type | Effect |
|---|---|---|
| `@ui/include_time` | boolean | Switches the input from `` to `` (equivalent to picking `datetime` as the component slug). |
| `@ui/date_format` | string | dayjs-style display format (`"MMM dd, yyyy"`, `"yyyy-MM-dd"`, etc). Display-only; storage stays ISO-8601 UTC. See [formatting](/docs/lcap/formatting) for the full token vocabulary. |
| `@ui/time_format` | string | dayjs-style time-portion format. |
| `@ui/timezone` | string | IANA tz (`"America/Los_Angeles"`). Applied at display only. |

## `field_type = "enum"`

| `@ui/component` | Effect |
|---|---|
| `select` *(default)* | Native `<select>` populated from `options_json`. |
| `radio` | Radio group. |
| `tags` | Reserved for a multi-select chip surface. |

The `options_json` column on the annotation row carries
`[{ value, label }, …]`. `` reads it directly.

## `field_type = "fsm_state_ref"`

Single-component; no overrides. When `options_json` carries a
curated state list, the resolver renders a `<select>`;
otherwise a free-text input.

## `field_type = "file_ref"`

Single-component; v0 renders a read-only summary
(`filename (size)`). The Page Designer Card block (rolling out) wraps `` with the existing
`` for full image / download rendering.

## `field_type = "relationship"`

| `@ui/component` | Effect |
|---|---|
| `display` *(default)* | Disabled `<input>` showing the id string. |
| `id_only` | Same render; reserved for future "show id with no chrome" behavior. |

| Key | Type | Effect |
|---|---|---|
| `@ui/display_path` | string | When set, the resolver runs a single `useEntity(target_entity, target_id)` fetch and shows the named field instead of the raw id. v0 doesn't traverse multi-level paths. |

> **Relationship is annotation-only in v0**
>
> `relationship` ships without inline editing, graph fetches, or
> multi-level traversal. Admins who need a relationship picker use
> LLM-authored extensions. A follow-on release may revisit once
> the v0 surface proves out.

## Cross-references

- [Field types](/docs/lcap/field-types) — the closed 9-type
  vocabulary and which slugs each type accepts.
- [Formatting](/docs/lcap/formatting) — the full dayjs token
  reference + locale resolution chain.
- [Annotations](/docs/lcap/annotations) — the admin editor
  surface that writes `ui_config_json`.
