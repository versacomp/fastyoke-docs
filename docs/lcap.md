---
title: LCAP
summary: Low-Code Application Platform — annotation-driven entity rendering. One annotation row drives Forms v2, Page Designer, CRUD scaffolds, and `@fastyoke/next` SSR pages.
order: 6
---

# LCAP — Low-Code Application Platform

LCAP is the runtime layer that turns FastYoke's schemaless entity
records into typed, formatted, validated UI without writing
per-field React components. It's built on three primitives:

1. **A 9-type field vocabulary** stamped onto
   `entity_field_annotations` — the closed set every consumer
   resolves against.
2. **`<SmartField />`** — a single React component in
   `@fastyoke/sdk` that walks an annotation row, picks the
   right input, applies the declared formatting, runs zod
   validation, and writes back.
3. **A cascading admin editor** — typing one field cascades the
   rest of the configuration so admins can't mis-annotate (a
   currency component requires a numeric type, etc.).

The same annotation row drives Forms v2, the Page Designer's
entity blocks, the CRUD scaffold's emitter, and any
`@fastyoke/next` consumer that mounts `<SmartField />`. **Edit the
annotation in one place; every surface follows.**

::section-cards{section="lcap"}
::

## When to use LCAP

LCAP sits alongside two other ways to ship entity-aware UI:

| Path | What you get | When to pick it |
|---|---|---|
| **LCAP** (this section) | Declarative — annotate once, render everywhere. Page Designer drag-drop binds blocks to `(entity, field_key)` pairs. | The default for entity-driven UI. Lowest authoring cost; admins can adjust formatting without a redeploy. |
| **CRUD scaffold builder** | Deterministic generator — pick annotated entities, install a multi-page list/detail/new/edit bundle. | "I want a complete admin for this entity right now." The bundle is editable post-install. |
| **LLM-authored extension** | Bespoke React extension written by Claude. Anything you can describe. | Custom flows that don't fit a list/detail pattern. |

LCAP and the CRUD scaffold compose: the scaffold's emitter
imports `<SmartField />` for every input, so the bundle stays
in lockstep with the LCAP type matrix as new types ship.

## The annotation row

`entity_field_annotations` is the source of truth. One row per
`(tenant_id, entity_name, field_key)`. Beyond label + required
+ length/range bounds, LCAP adds three columns:

- **`field_type`** — closed 9-type enum: `string`, `longtext`,
  `number`, `boolean`, `timestamp`, `enum`, `fsm_state_ref`,
  `file_ref`, `relationship`. Pre-LCAP rows have `NULL` and
  degrade to `string`. See [field types](/docs/lcap/field-types).
- **`ui_config_json`** — free-form JSON object whose `@ui/*` keys
  drive component selection + formatting. See
  [ui-config reference](/docs/lcap/ui-config).
- **`display_order`** — integer; drives Card-block sort order
  in the Page Designer.

The columns are nullable. Existing tenants who never touch LCAP
keep working — the resolver falls back to the same inferred
behavior the pre-LCAP surfaces showed.

## The resolver — `<SmartField />`

`<SmartField />` is the only consumer of an annotation row. Forms
v2, Page Designer entity blocks, the CRUD scaffold, Workspace —
everything routes through it. Public API:

```tsx
import { SmartField } from '@fastyoke/sdk';

<SmartField
  annotation={annotation}        // EntityFieldAnnotation
  value={record.data_payload[field_key]}
  onChange={(next) => update(field_key, next)}
  mode="edit"                    // 'edit' | 'display'
  density="comfortable"          // 'compact' | 'comfortable' | 'spacious'
  uiConfigOverride={{ '@ui/date_format': 'yyyy' }}  // optional
  currentTier="team"             // tier-gates @ui/visible_when
/>
```

`mode="display"` short-circuits to a `<span>` with the formatted
value — no input, no zod schema, no expression evaluation. Use
for read-only surfaces (table cells, summary cards). `mode="edit"`
mounts the appropriate input from the catalog and runs zod
validation on change.

`uiConfigOverride` layers AFTER `annotation.ui_config_json` so a
single block can specialize one rendering surface (e.g. a summary
card showing year only) without mutating the shared annotation
row.

## Tier gates

The resolver is **free for all tenants** — annotated fields
render correctly on Hobby and Pro. Three Page-Designer surfaces
require **Team tier** or higher:

- The cascading inspector inside Page Designer / Workspace
- Drag-drop morphing of entity blocks
- The QuickJS expression runtime (`@ui/visible_when`,
  `@ui/compute`, `@ui/validate_when` — shipping in a follow-on
  sub-phase)

See [pricing](/pricing) for the full tier matrix; see
[advanced app builder](/docs/apps/advanced) for the
existing Team+-gated surfaces.

## What's in this section

- [Field types](/docs/lcap/field-types) — the closed 9-type
  vocabulary + the type-to-component matrix.
- [Annotations](/docs/lcap/annotations) — the admin editor, the
  cascade FSM, advanced sub-row config, display order.
- [UI config reference](/docs/lcap/ui-config) — every `@ui/*`
  key the resolver respects, per type.
- [Formatting](/docs/lcap/formatting) — currency, percent, date,
  time, locale resolution, dayjs token vocabulary.
- [Page Designer integration](/docs/lcap/page-designer) — the
  `entity_field` block and how it composes with `<SmartField />`.

::callout{type="tip" title="LCAP — fully shipped (2026-04-26)"}
All sub-phases are live, including the Forms v2
internal SmartField switch. In the admin live preview, fields
stamped with an `entity_field` source route through
`<SmartField />` when the form/annotation type pair is on the
strict allow-list — `text↔string`, `textarea↔longtext`,
`number↔number`, `checkbox↔boolean`, `date↔timestamp`,
`select↔enum`, `file↔file_ref`. Mismatches and missing
annotations fall back to the legacy renderer with a one-time
`console.warn`. The public form renderer stays on the legacy
path until a public-annotation read endpoint is designed.
::
