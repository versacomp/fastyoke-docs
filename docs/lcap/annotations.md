---
title: Annotations
summary: Edit field annotations from `/admin/entities/:name`. Type picker cascades — changing field_type prunes invalid component slugs and config keys automatically.
---

# Annotations

Annotations are the metadata layer over schemaless entity
records. The admin editor at
**`/admin/entities/:entityName/annotations`** is where every
LCAP-aware field gets typed, labeled, formatted, and
validated.

This page documents the editor surface, the cascade behavior
when you change `field_type`, and the advanced sub-row that
holds per-type config.

## What the editor shows

Each row corresponds to one `(entity_name, field_key)` pair.
Columns:

| Column | Purpose |
|---|---|
| **Field key** | The JSON key in `entity_records.data_payload`. Free-text; pre-existing fields auto-populate from sampled records. |
| **Label** | Human-facing display label. Falls back to a Title-Cased version of the key. |
| **Type** | The closed 9-type vocabulary. `(none)` keeps the row pre-LCAP. |
| **Component** | `@ui/component` slug, filtered by Type. Disabled until a Type is picked. |
| **Required** | Forces presence at validation time. |
| **Max length** | String / longtext only — disabled for other types. |
| **Min / Max** | Number only. |
| **Order** | `display_order` — drives Card block sort order in the Page Designer. NULL sinks to the end (alphabetical by key). |
| **Help text** | Tooltip / form-hint string surfaced by Forms v2 + Page Designer. |

Below each row, an expandable **Advanced** sub-row shows
per-type config (currency code, decimal places, date format,
timezone, options JSON, etc.). Only the keys applicable to the
current type render — switch from `number` to `timestamp` and
`@ui/currency_code` disappears, `@ui/include_time` appears.

## The cascade

Changing `field_type` runs `cascadeAnnotation(row, newType)` —
a pure function that:

1. **Drops invalid `@ui/component`.** If the previous slug
   isn't in the new type's allowed list, it gets removed; the
   resolver falls back to the new type's default.
2. **Prunes type-irrelevant `ui_config_json` keys.**
   Switching `number → timestamp` removes
   `@ui/currency_code`, `@ui/decimal_places`,
   `@ui/use_separators`. Switching `timestamp → number`
   removes `@ui/include_time`, `@ui/date_format`,
   `@ui/timezone`.
3. **Preserves expression keys.** `@ui/visible_when`,
   `@ui/compute`, `@ui/validate_when` survive every type
   transition — they aren't type-specific.
4. **Reflows numeric / length bounds.** `max_length` clears
   for non-stringy types; `min` / `max` clear for non-numeric
   types.

The cascade is purely UI-side until you click **Save**; cancel
or revert restores the pristine snapshot from the last
successful load. No partial-write state survives a network
failure.

::callout{type="tip" title="Revert is non-destructive"}
The Revert button restores the row state from the last
successful load or save — no API call, no flicker. Use it
freely while exploring different type / component combos.
::

## Save semantics

The PUT handler at
`/api/v1/tenant/entities/:name/annotations` takes the **full
annotation set** and reconciles:

- Rows in the request body but not the DB → INSERT.
- Rows in the DB but not the request body → DELETE.
- Rows in both → UPDATE.

So a single Save reflects the editor's final state; partial
edits aren't possible. If you only want to remove one row,
delete it from the editor first then Save.

## Where annotations come from

The editor populates with two sources merged:

1. Existing `entity_field_annotations` rows for the entity.
2. Up to 50 sampled `entity_records` for the entity, with
   `deriveSchema` extracting every observed field key. Keys
   that exist in records but lack annotations render as blank
   rows you can fill in.

Keys that have annotations but no longer appear in records
stay listed — so an admin can see + clean up stale
annotations deliberately.

## Tier gating

The editor itself is **free for all tenants** at
`/admin/entities/:entityName/annotations`. The same component
embedded in the Page Designer right-rail inspector + Workspace
inspector requires **Team tier or higher**; Solo and Pro
admins continue to use the standalone admin editor.

## Cross-references

- See [field types](/docs/lcap/field-types) for the
  9-type matrix and the type-to-component allow-list.
- See [ui-config reference](/docs/lcap/ui-config) for every
  `@ui/*` key the Advanced sub-row exposes.
- See [Page Designer integration](/docs/lcap/page-designer)
  for how annotated fields render at runtime.
