# Page Designer integration

> The `entity_field` block binds a Page Designer block to one (entity, field, record) tuple and mounts `<SmartField mode="display" />`. Annotation drives display formatting — change once, every block follows.

# Page Designer integration

The Page Designer's `entity_field` block is the
first surface where LCAP rendering meets the workspace. Drag
the block onto the canvas, pick an entity + field + record
binding, and the workspace renders the field via
`` — display formatting flows
straight from the annotation row.

This page documents the block shape, the editor surface, and
the renderer's fetch path.

## Block shape

`pages.layout_json` carries the block as:

```json
{
  "id": "<random>",
  "type": "entity_field",
  "config": {
    "entity_name": "customer",
    "field_key": "amount",
    "record_id": "first",
    "label": "Outstanding balance",
    "ui_config_override": {
      "@ui/currency_code": "EUR"
    }
  }
}
```

| Field | Type | Required | Notes |
|---|---|---|---|
| `entity_name` | string | yes | The entity whose field gets rendered. Falls into the existing tenant-scoped `/api/v1/tenant/entities/:name` endpoint. |
| `field_key` | string | yes | The field on `data_payload`. Must match an annotation row for nice rendering, but the renderer falls back to a permissive string display when no annotation exists. |
| `record_id` | string | yes | Either a literal record id (`"cust-42"`) or the special string `"first"` — see below. |
| `label` | string | optional | A small uppercase caption above the value. Empty omits the caption. |
| `ui_config_override` | object | optional | `@ui/*` keys layered AFTER `annotation.ui_config_json`. Lets one block specialize one rendering surface (e.g. summary card showing year only) without mutating the shared annotation row. |

## The editor surface

The Page Designer right-rail editor for `entity_field` shows
three inputs:

1. **Entity name** — free text (so admins can author the
   binding before the entity has any records).
2. **Field key** — a `<select>` populated from the entity's
   annotated fields when annotations exist; falls back to a
   free-text input otherwise. The dropdown shows
   `<field_key> · <label>` so admins can pick by either name.
3. **Record id** — free text. `first` is the documented
   special value.
4. **Label** — optional caption above the value.

The full annotation editor (cascading inspector with type +
component pickers + advanced sub-row) **doesn't** live here in
v0 — that surface is at
[/admin/entities/:name/annotations](/docs/lcap/annotations).
A planned follow-on ships the cascading inspector inside the
Page Designer right-rail too (Team-tier gated).

## `record_id = "first"`

For entities that model singletons — settings rows, profile
records, the tenant's own metadata — `record_id: "first"` tells
the renderer to fetch `/api/v1/tenant/entities/:name?limit=1`
and pull `records[0]`. The block stays bound to "the first
record this entity has" rather than a literal id, which is
useful for admin pages that need to show a fixed record without
hard-coding its UUID.

For entities with many records, use a literal id and accept
that the binding tracks one specific record. A future
follow-on may add a record-context binding (e.g.
"the record this page was navigated to") once the workspace's
record-aware routing lands.

## Render path

The workspace's `EntityFieldRenderer` fetches in parallel:

1. Annotations:
   `GET /api/v1/tenant/entities/:name/annotations?tenant_id=…`
2. Record:
   - `GET /api/v1/tenant/entities/:name?tenant_id=…&limit=1`
     when `record_id === "first"`
   - `GET /api/v1/tenant/entities/:name/:id?tenant_id=…`
     for a literal id

Then it picks `annotations.find(a => a.field_key === field_key)`,
defaulting to `{ field_key }` (a minimal annotation) when none
exists, and mounts:

```tsx
<SmartField
  annotation={ann}
  value={record.data_payload[field_key]}
  onChange={() => {}}
  mode="display"
  uiConfigOverride={config.ui_config_override}
/>
```

`onChange` is a no-op because the v0 surface is **read-only by
design** — Page Designer pages aren't form-builder surfaces.
The Card block will add an explicit edit toggle in a future release.

## The Card block

The `entity_card` block is the "show every annotated field of
one record" companion. Drag it onto the canvas, pick an entity
+ `record_id`, and the workspace iterates the entity's
annotation list at render time — one
`` per row, sorted by
`display_order` (NULLs sink to the end, alphabetical by
field_key among ties; same convention as the admin editor and
the backend's `fetch_annotations` ORDER BY).

```json
{
  "id": "<random>",
  "type": "entity_card",
  "config": {
    "entity_name": "customer",
    "record_id": "first",
    "label": "Customer profile"
  }
}
```

The Card has no field-key picker — it shows everything
annotated for the entity. For subset rendering, compose
multiple `entity_field` blocks instead. Empty-annotations
case shows an inline hint pointing the admin at
`/admin/entities/<entity>/annotations`.

## CRUD scaffold integration

The deterministic CRUD bundle generator
(`/admin/apps` → Generate, mode "CRUD scaffold") now emits
bundles that import `` for every form input and
detail cell. Each entity's annotations get serialized inline as
a `const _ANNOTATIONS = [...]` JS array; per-field
`SmartField` calls reference the matching annotation row at
render time.

Effects on emitted bundles:

- **Form inputs** stay in lockstep with the LCAP type matrix.
  Adding a new type (or a new `@ui/component` slug) ships with
  the SDK; bundles re-emit through SmartField pick up the new
  rendering automatically — no per-bundle markup.
- **Detail cells** render with locale-aware currency / date /
  percent formatting (matrix-driven). Pre-LCAP rows still work
  — `` falls back to a permissive string
  rendering when `field_type` is `NULL`.
- **List cells** stay on `FilePayloadView` for the 80px image-
  thumbnail behavior. SmartField's file-summary span doesn't
  currently replicate it; a future release may unify.

Re-emitting a bundle (admin clicks Generate again) replaces
pre-LCAP output; existing installed bundles keep working
unchanged. See [Studio wizard](/docs/apps/studio-wizard) for the
full overview.

## What's not yet shipped

- **Cascading inspector embedded in Page Designer / Workspace**
  — currently editor-only; embedding is planned and will be
  Team-tier gated.

The **Forms v2 internal SmartField switch** previously listed here
has shipped — see the LCAP overview for the strict allow-list of
form/annotation type pairs (`text↔string`, `textarea↔longtext`,
etc.) that route through ``.

## Cross-references

- [Field types](/docs/lcap/field-types) — drives what the
  renderer mounts per binding.
- [Formatting](/docs/lcap/formatting) — how the resolver picks
  Intl + dayjs options.
- [Pages](/docs/pages) — the broader Page Designer reference.
