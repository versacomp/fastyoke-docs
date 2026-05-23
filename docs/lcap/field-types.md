---
title: Field types
summary: The closed 9-type vocabulary every LCAP consumer resolves against. Type drives the default component; @ui/component overrides it within the type's allowed slug list.
---

# Field types

Every LCAP-aware annotation row carries a **closed 9-type
vocabulary** in `field_type`. The resolver maps each type to a
default React component; `ui_config_json["@ui/component"]`
overrides the default within the type's allowed slug list.

The vocabulary is closed by design — adding a new type ships
with the SDK, not at runtime. Admins who need a fully custom
component author an LLM-authored extension instead.

## The matrix

| `field_type`     | Default component         | Wire shape                         | Notes |
|---|---|---|---|
| `string`         | `<TextInput />`           | string (≤ `max_length`)            | Single-line text. |
| `longtext`       | `<TextArea />`            | string (no length cap unless declared) | Multi-line. |
| `number`         | `<NumberInput />`         | number                             | Honors `min`/`max`/`@ui/step`. |
| `boolean`        | `<Checkbox />`            | boolean                            | |
| `timestamp`      | `<DatePicker />`          | ISO-8601 string                    | Storage stays UTC; display via `@ui/timezone`. |
| `enum`           | `<Select />`              | string from `options_json`         | |
| `fsm_state_ref`  | `<FsmStatePicker />`      | string (state name)                | Free-text input by default; `<select>` when `options_json` carries a curated list. |
| `file_ref`       | summary span              | `FileRef` JSON                     | Read-only summary; rich preview lives in the Card block. |
| `relationship`   | `<RelationshipDisplay />` | string (id) or `{ id, display_path }` | **Annotation-only in v0.** No graph fetch, no inline picker. |

`NULL` `field_type` (pre-LCAP rows) degrades to `string` —
existing tenants who haven't started using LCAP keep working.

## `@ui/component` overrides per type

When the default component isn't enough, set
`ui_config_json["@ui/component"]` to one of the allowed slugs.
Mismatched combos are rejected at write time (the backend's PUT
validation refuses, returning 422) and at render time (the
resolver console-warns and falls back to the type's default).

| `field_type` | Allowed `@ui/component` slugs |
|---|---|
| `string`     | `text` *(default)*, `password`, `email`, `url`, `phone`, `slug` |
| `longtext`   | `textarea` *(default)*, `richtext`, `markdown`, `code` |
| `number`     | `number` *(default)*, `currency`, `percent`, `slider`, `rating` |
| `boolean`    | `checkbox` *(default)*, `switch`, `radio` |
| `timestamp`  | `date` *(default)*, `datetime`, `time`, `daterange` |
| `enum`       | `select` *(default)*, `radio`, `tags` |
| `fsm_state_ref` | `fsm_state_picker` |
| `file_ref`   | `file_payload` |
| `relationship` | `display` *(default)*, `id_only` |

`richtext` / `markdown` / `code` resolve to lazy peer packages
(`@fastyoke/lcap-richtext`, `@fastyoke/lcap-markdowneditor`,
`@fastyoke/lcap-codeeditor`). When the peer package isn't
installed, the resolver falls back to a plain `<TextArea />`
with a one-time `console.warn`.

::callout{type="info" title="Heavy-editor peer packages — installed separately"}
The richtext / markdown / code components ship as separate
npm packages so the SDK base bundle stays small. Install
`@fastyoke/lcap-richtext`, `@fastyoke/lcap-codeeditor`, or
`@fastyoke/lcap-markdowneditor` to enable the rich
rendering for the matching `@ui/component` slug. Without
the package installed, `<SmartField />` falls back to a
plain `<TextArea />` with a one-time console warning;
content shape (HTML / markdown / source) stays compatible
across the swap.
::

## Storage shapes

Storage shape is the **wire format** — what lands in
`entity_records.data_payload[field_key]`. Display formatting is
applied by the resolver only at render time; storage stays
canonical so an export script that bypasses `<SmartField />`
gets sortable, parseable values.

- **`string`, `longtext`** — UTF-8 string. The legacy `text`
  alias for `longtext` is still accepted by the SDK helper.
- **`number`** — JSON number. Currency components render with
  `Intl.NumberFormat` at display time; the stored value is the
  raw number. Percent components store decimal (`0.07`); the
  display layer multiplies by 100 for the user.
- **`boolean`** — JSON boolean.
- **`timestamp`** — ISO-8601 UTC string. `@ui/timezone` shifts
  display only.
- **`enum`** — string from `options_json[].value`. Empty
  `options_json` falls back to a permissive string schema.
- **`fsm_state_ref`** — string state name. Same wire shape as a
  free-text field; the rendering hint is what makes it special.
- **`file_ref`** — object: `{ ref, filename?, mime?, size_bytes? }`.
  Matches the shipped Forms v2 file-upload pipeline. Optional
  uploads accept `null`.
- **`relationship`** — string id (typically a record id from
  another entity).

## zod refinements

The shipped `entityAnnotationToZod` helper (`@fastyoke/sdk`)
maps each type to the right zod leaf with constraints from the
annotation row applied:

- `required: true` → `z.string().min(1)` for stringy types,
  `z.number()` (must be present) for numeric.
- `max_length` → `.max(n)` on string leaves.
- `min` / `max` → `.min(...).max(...)` on number leaves.
- `options_json` (for enum) → `z.enum([...values])`.
- `timestamp` → string with a `Date.parse` refinement so a
  malformed value surfaces before the write hook fires.
- `@ui/component = "percent"` → `[0, 1]` storage range by
  default; explicit `min`/`max` overrides for "growth %"
  cases.
- `@ui/decimal_places: N` → `parseFloat(v.toFixed(N)) === v`
  refinement (rejects extra precision; handles IEEE-754 quirks
  like `0.07.toFixed(2) === '0.07'`).
- `file_ref` → `z.object({ ref, filename?, mime?, size_bytes? })
  .nullable()`.
- `fsm_state_ref` / `relationship` → string id (with
  `.min(1)` when `required`).

## Cross-references

- See [annotations](/docs/lcap/annotations) for how to set
  `field_type` + `ui_config_json` from the admin UI.
- See [formatting](/docs/lcap/formatting) for the full set of
  number / timestamp formatting keys.
- See [ui-config reference](/docs/lcap/ui-config) for the
  exhaustive list of `@ui/*` keys.
