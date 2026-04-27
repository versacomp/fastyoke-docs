# CRUD Scaffold Builder

> Deterministic template-driven CRUD generator — pick annotated entities, install a ready-to-use list/detail/new/edit extension.

# CRUD Scaffold Builder

The **CRUD scaffold** is a third extension-generation path on the
Advanced App Builder, alongside the shipped **Template** and
**LLM-authored** paths. It produces a deterministic multi-page
CRUD extension from a tenant's annotated entities — no LLM, no
prompt drift, same inputs produce the same bundle byte-for-byte.

Use it when you want **standard CRUD** for your entities. Use the
LLM path instead when you want **bespoke UI shaped by prose**.

## When to reach for it

- You have one or more entities the app will read and write.
- Each entity is annotated (via `/admin/entities/:name/annotations`).
- You want the output to be predictable — the same pick + same
  annotation set produces the same extension every time.

If you want a custom home screen, dashboards, or anything that
doesn't fit "list → detail → form", use the LLM path or author the
bundle by hand. The scaffold is **on rails** — that's the whole
point.

## Prerequisites — annotate your entities first

The picker sources its list from `entity_field_annotations`. An
entity with records but no annotations is invisible to the picker
(the scaffold needs labels, types, required flags, and enum
options to render meaningful pages).

Annotate at `/admin/entities/<entity_name>/annotations`. Give each
field a label, mark required fields, and fill in `options_json` for
enum fields. The picker surfaces the field + required counts so
you can tell at a glance whether an entity is ready.

## Generate a CRUD bundle

1. Navigate to **Advanced** on any app (`/admin/apps/:id/advanced`).
2. Under **Generate extension**, pick **CRUD scaffold**.
3. Check each entity whose CRUD pages you want in this bundle.
4. Click **Install bundle** (or **Download bundle zip** if you want
   to iterate locally).
5. A preview modal surfaces the generated source plus the required
   scopes (`data:read` + `data:write`). Confirm to install.
6. Activate from `/admin/extensions` once you've reviewed the code.

## What the bundle contains

Per selected entity, four components:

| Page | Hook it uses | What it renders |
|---|---|---|
| `{Entity}ListPage` | `useEntities(kind)` | Sortable table with columns from annotations + per-row **View** / **Delete** buttons + **New** button |
| `{Entity}DetailPage` | `useEntity(kind, id)` | Read-only `<dl>` of annotated fields + **Edit** / **Delete** buttons |
| `{Entity}NewPage` | `useCreateEntity(kind)` | Form with one input per annotation, client-side required + max_length enforcement |
| `{Entity}EditPage` | `useEntity` + `useUpdateEntity` | Same form, pre-filled from the existing record |

Plus a top-level `HomePage` that links to each entity's list, and
a `Page` router that dispatches between them via React state.

The manifest declares a single page at `/<slug>` because internal
navigation happens inside the bundle — the extension platform
doesn't need to know about sub-routes.

## Scopes

The scaffold always stamps `required_scopes: ["data:read",
"data:write"]` verbatim. CRUD always writes; there's no scope
inference to run (that's the LLM path's concern).

## What the scaffold doesn't do (v0)

- **Cross-entity relationships.** If you pick `Customer` + `Order`
  and Order has a `customer_id` field, v0 renders it as a plain id.
  No inline customer detail, no link to the Customer page. Edit
  the bundle post-install if you need linking.
- **Search / sort / filter** beyond what the `useEntities` envelope
  exposes. Advanced table behaviour is admin-edits-bundle.
- **Bulk operations.** Delete is per-row with a confirm prompt; no
  multi-select.
- **Custom field components.** Annotation `field_type` maps 1:1 to
  a shipped input (`string` → text, `number` → numeric, `boolean`
  → checkbox, `enum` → select). Custom types fall back to text.
- **Per-page RBAC inside the bundle.** Any user the extension's
  scopes grant can see every page.
- **LLM polish.** Deterministic output is the point. If you want
  LLM-authored copy or layouts, use the LLM path.

## Picking the right path

| You want… | Use |
|---|---|
| Standard CRUD for Customer + Order | **CRUD scaffold** |
| A bespoke admin dashboard from prose | **LLM-authored** |
| A working starter to hand-edit | **Template** |
| Just embed an FSM viewer in your own Next.js app | [`@fastyoke/sdk` ``](/docs/sdk/reference) |

## Editing the generated bundle

The scaffold is your starting point, not your ceiling. After
install, download the bundle zip from the Extensions page and edit
`index.mjs` to:

- Add custom columns or row actions to a list page.
- Swap the default form inputs for richer components.
- Introduce a dashboard alongside the per-entity pages.
- Link related entities together.

Re-install the edited bundle as a new extension version. Scopes
stay at whatever the manifest declares — you can widen them in the
JSON before re-upload if you add new hooks.

## Related

- **[SDK CRUD UI recipe](/docs/recipes/sdk-crud-ui)** — for
  building a CRUD page in your own Next.js app (outside the
  extension surface) with the same SDK hooks.
- **[Advanced App Builder](/docs/apps/advanced)** — the full
  surface the CRUD scaffold lives inside.
- **[Entities](/docs/entities)** — annotation model the scaffold
  reads from.
