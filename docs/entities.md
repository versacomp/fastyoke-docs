---
title: Entities
summary: Schemaless JSON records that serve as FSM guard-evaluation context.
order: 4
---

# Entities

Entity records are how FastYoke holds your domain data. They're
intentionally schemaless — every record is a JSON payload stored in
`entity_records.data_payload` as TEXT. You don't declare fields up
front; you store whatever your workflow needs.

::section-cards{section="entities"}
::

## Why schemaless

Different tenants run different logistics domains. A delivery
company's "shipment" record looks nothing like a field-service
provider's "work order". Forcing them into a shared column
vocabulary would either flatten every tenant's needs into a lowest
common denominator, or explode into a nightmare of per-tenant
migrations.

FastYoke sidesteps that by:

- Storing every record's payload as opaque JSON.
- Sampling records at read time to derive a display schema (the
  "entity picker" in Forms + Page Designer).
- Exposing the JSON as `{ "var": "name" }` to JSONLogic guards,
  so FSM conditions evaluate against the real payload.

## Annotations

The schemaless default is pragmatic but thin on metadata: you can't
infer "this field is required" or "this field is an enum of three
options" from sampled records alone. The annotation layer fills
that gap.

Click **Annotations →** from the Entities browser to open a bulk
editor. Per-`(entity_name, field_key)` you can set:

- `label` — display override (falls back to a humanized key)
- `required` — true/false
- `max_length`, `min`, `max` — scalar constraints
- `options_json` — enum choices as `[{value, label}, ...]`
- `help_text` — surfaced as hover tooltips in picker + guard
  composer

Annotations are **additive, not constraining**. They never block a
write to `entity_records.data_payload` — they only enrich the
surfaces that read the schema (Forms picker, Page Designer block
config, FSM guard composer).

## Who reads annotations

| Surface | What it does with them |
|---|---|
| Forms Builder entity picker | Picks the annotated label; synthesizes `select` from `options_json`; copies `required` + `max_length` + `min`/`max` onto the new FormField. |
| Page Designer `entity_list` | Renders `Columns` as a checkbox picker with annotated labels; `Default sort field` as a dropdown. `help_text` shows as hover tooltips. |
| FSM guard composer | When the schema is bound to an entity, attaches a `<datalist>` of annotated field keys to the Variable path input. `help_text` surfaces as a tooltip on the populated value. |

## Files as entity data

Uploaded files attached to entity records live in the `entity_files`
table — stored as a SQLite BLOB inside the per-tenant DB, not on
disk. That means they ride along with the tenant's Litestream
replica automatically, and no cross-tenant leak is possible at the
filesystem level.

Public form attachments are a separate table + on-disk layout
because they're uploaded anonymously; see the [Forms Builder
guide](/docs/forms/builder).
