---
title: Studio wizard
summary: Wix-style 4-step builder — pick a layout, brand it, choose entities, ship a themed multi-page extension.
order: 3
---

# Studio wizard

The **Studio wizard** is the standard path for generating an app
extension on the Advanced App Builder. It produces a deterministic,
themed, multi-page React/Tailwind bundle from your tenant's
annotated entities — same picks produce the same bytes every time,
zero LLM involvement.

Use it for **standard CRUD plus workflow controls**. Use the
[LLM-authored path](/docs/apps/advanced#synth-paths) instead when
you want bespoke UI shaped by prose.

## When to reach for it

- You have one or more entities the app will read and write.
- Each entity is annotated (via `/admin/entities/:name/annotations`).
- You want the output to be predictable — same picks, same layout,
  same brand → identical bundle.

If you want a custom dashboard, marketing-style landing pages, or
anything that doesn't fit "list → detail → form per entity", use
the LLM path or author the bundle by hand. The wizard is **on
rails** — that's the whole point.

## Prerequisites — annotate your entities first

The wizard's entity-pick step sources its list from
`entity_field_annotations`. An entity with records but no
annotations is invisible (the wizard needs labels, types, required
flags, and enum options to generate meaningful pages).

Annotate at `/admin/entities/<entity_name>/annotations`. Give each
field a label, mark required fields, fill in `options_json` for
enum fields. The picker surfaces the field + required counts so
you can tell at a glance whether an entity is ready.

## Walkthrough

From `/admin/apps/:id/advanced`, pick **Studio** in the synth
selector and click **Open Studio…**. The wizard modal opens with
four steps in the left rail.

### Step 1 — Layout

Pick the chrome the generated app wears:

| Layout | Best for |
|---|---|
| **Left nav** | 5+ entities, longer labels, internal admin tools. Vertical sidebar with the brand color. |
| **Top nav** | ≤5 entities, marketing-app feel. Horizontal bar across the top. |
| **Top bar + side rail** | Branded top bar (logo + user menu) plus a secondary entity rail. Most "Wix-app" of the three. |

The center pane shows a live preview of the chosen layout against
your current branding. Switch layouts and watch the preview update.

### Step 2 — Branding

A gallery of preset palettes (Slate Pro, Midnight, Citrus, Rose,
Forest, Monochrome) — click one and your preview is fully styled.
Each preset bundles a primary color, accent color, and a font
family from a curated list (Inter, Source Sans 3, IBM Plex Sans,
Geist).

Need something specific? Click **Customize** to expose a drawer
with `<input type="color">` for primary + accent, a font dropdown,
a light / dark toggle, and an optional logo upload (the data URL
becomes the `--brand-logo-url` token).

Customizing detaches you from the preset; selecting a preset
afterwards re-applies the preset's colors over your edits.

### Step 3 — Entities

A checklist of every annotated entity in your tenant. Tick the
ones the app should manage. Each ticked entity will get a list +
detail + new + edit page. Entities with FSM schemas additionally
get the workflow viewer + advance-state controls on their detail
page.

The picker surfaces the annotation count alongside each entity's
name so you can spot under-annotated entities at a glance.

### Step 4 — Review

A summary of your picks, plus a **Theme name** field that defaults
to `<App> theme` (editable). Click **Install** to ship it.

## What Install does

The wizard modal collects everything client-side without hitting
the server. Only **Install** triggers the network sequence:

1. `POST /api/v1/tenant/themes` — materializes your branding as a
   tenant theme record. The theme becomes a first-class row at
   `/admin/themes` you can edit, fork, or assign to other apps
   later.
2. `POST /api/v1/tenant/apps/:id/advanced/generate-extension` —
   generates the bundle with `synth: "studio"`, the new theme id,
   the chosen layout, and the ticked entity names. Inserts a
   `tenant_extensions` row with `is_active = 0`.

Both calls are scoped to the authenticated tenant. Theme creation
fails → error inline, no generate attempt. Generate fails after
theme creation → error inline naming the orphaned theme so you
can delete it from `/admin/themes` (or just retry — the same
theme name will collide and surface the error, so adjust the name
in Review and retry).

## What the bundle contains

Per ticked entity, four components:

| Page | Hook it uses | What it renders |
|---|---|---|
| `{Entity}ListPage` | `useEntities(kind)` | Table with columns from annotations + per-row **View** button + **New** button. |
| `{Entity}DetailPage` | `useEntity(kind, id)` | `<dl>` of `<SmartField mode="display">` per annotated field; if the entity has an FSM schema, additionally mounts `<WorkflowSection>` for live state + advance + history. |
| `{Entity}NewPage` | `useCreateEntity(kind)` | Form with one `<SmartField mode="edit">` per writable annotation, client-side required + max_length enforcement. |
| `{Entity}EditPage` | `useEntity` + `useUpdateEntity` | Same form, pre-filled from the existing record. |

Plus a top-level `Page` export that wraps a `useState`-driven
router inside the chosen shell component (`LeftNavShell`,
`TopNavShell`, or `TopBarSideRailShell`), each importing its
theme tokens via `<ThemeStyle>` scoped to `[data-theme={themeId}]`.

The manifest declares a single page at `/<slug>` because internal
navigation happens inside the bundle — the extension platform
doesn't need to know about sub-routes.

## The workflow viewer

Detail pages of FSM-bearing entities mount the SDK's
`<WorkflowSection>` below the field grid. It renders:

- **Current state badge** — pill showing the active job's state.
- **Breadcrumb of states** — textual flow `Draft → [Submitted] →
  Approved → Shipped` with the current state bracketed.
- **Advance bar** — one button per legal outgoing transition from
  the current state. Click → optimistic state update → on 409 /
  422 the badge reverts and an inline error toast renders.
- **History** — most recent 50 event_log entries, newest-first.
  Admin-cancel rows render their `reason` text in a quieter
  italic style (the audit ledger is append-only and immutable —
  see [Workflows → admin override](/docs/workflows#admin-override)).

When the entity has an FSM schema but no active jobs, the section
collapses to a "Start workflow" CTA. When there's no schema bound
to the entity, the section renders nothing.

## Scopes

Studio bundles always stamp `required_scopes: ["data:read",
"data:write"]` verbatim — CRUD pages always write. There's no
scope inference (that's the LLM path's concern, since LLM output
varies).

## What the wizard doesn't do (today)

- **Cross-entity relationships.** If you pick `Customer` + `Order`
  and Order has a `customer_id` field, today's bundle renders it
  as a plain id. No inline customer detail, no link to the
  Customer page. Edit the bundle post-install if you need linking.
- **Per-entity field selection.** Every annotated field shows in
  every page. To trim a list view to just key columns, edit the
  bundle after install.
- **Custom field components.** Annotation `field_type` maps 1:1
  to a SmartField input (`string` → text, `number` → numeric,
  `boolean` → checkbox, `enum` → select). Custom types fall back
  to text.
- **Search / sort / filter** beyond what `useEntities` exposes.
  Advanced table behaviour is admin-edits-bundle.
- **Bulk operations.** No multi-select.
- **Per-page RBAC inside the bundle.** Any user the extension's
  scopes grant can see every page.
- **Reactflow-based FSM graph.** The workflow viewer is textual
  (breadcrumb + buttons + history). A graph rendering would drag
  reactflow into every emitted bundle (~150KB gzipped); the
  trade-off didn't justify the cost.

## Picking the right path

| You want… | Use |
|---|---|
| Themed multi-page CRUD app, predictable output | **Studio wizard** |
| Bespoke admin dashboard from prose | [**LLM-authored**](/docs/apps/advanced#synth-paths) |
| Just embed an FSM viewer in your own Next.js app | [`@fastyoke/sdk` `<WorkflowSection>`](/docs/sdk/reference) |

## Editing the generated bundle

Studio's output is your starting point, not your ceiling. Download
the bundle zip from the Extensions page and edit `index.mjs` to:

- Add custom columns or row actions to a list page.
- Swap a default `<SmartField>` for a richer component.
- Introduce a dashboard alongside the per-entity pages.
- Link related entities together.

Re-install the edited bundle as a new extension version. Scopes
stay at whatever the manifest declares — widen them in the JSON
before re-upload if you add new hooks.

## Related

- **[Advanced App Builder](/docs/apps/advanced)** — the surface
  the Studio wizard lives inside.
- **[Themes](/admin/themes)** — where the wizard's branding lands
  as a first-class row you can edit and reuse.
- **[Entities](/docs/entities)** — annotation model the wizard
  reads from.
- **[Workflows](/docs/workflows)** — FSM schemas surfaced through
  the workflow viewer on detail pages.
- **[SDK CRUD UI recipe](/docs/recipes/sdk-crud-ui)** — building
  CRUD UI in your own Next.js app outside the extension surface
  with the same SDK hooks.
