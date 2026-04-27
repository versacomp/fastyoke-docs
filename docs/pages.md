# Pages

> Block-based workspace pages that embed FastYoke data into your operator UI.

# Pages

Pages are how admins compose custom workspace views without writing
code. Each page is an ordered list of blocks (headings, paragraphs,
lists, action buttons, images) configured through a visual designer,
then rendered at a tenant URL. When a page is marked public, the
same render is available at `/p/:slug` — useful for driver-facing
dashboards, customer portals, or anything you'd otherwise need to
author a full extension for.

## Page vs extension

| I want... | Use |
|---|---|
| List jobs from a schema, fire transitions on a button click | A **page** |
| Custom UI beyond the block catalog (charts, maps, tables) | An **extension** |
| Something anonymous users can read | A **public page** |
| Rich interactivity tied to tenant state | An **extension** |

The boundary is intentional: pages trade customization for
configurability. An extension is a React bundle you author + upload;
a page is clicks in the designer + immediate preview.

## Block types

The designer ships six block types. More can be added via
extensions (the SDK's `registerBlock()` API — see
[SDK Reference](/docs/sdk/reference#block-registration)).

| Type | Purpose |
|---|---|
| `heading` | H1 / H2 / H3 with text content |
| `paragraph` | Prose paragraph |
| `job_list` | List jobs for a given FSM schema, optionally filtered |
| `entity_list` | List entity records for a given entity kind |
| `action_button` | Fires a named FSM transition on click |
| `image` | Image served from `entity_files` (one upload per image block) |

The `entity_list` block is the bridge to
[entity annotations](/docs/entities) — when the typed entity has
annotations, Columns renders as a checkbox picker and Default sort
field becomes a dropdown populated from annotated field keys.

## The designer workflow

1. Open **Pages** in the admin sidebar and click **New page**.
2. Give it a `name` and a URL-safe `slug` (used in the route).
3. Drag blocks from the palette into the canvas. Each block opens
   a config panel — field keys, labels, schema ids.
4. Toggle **Public** if you want the page served at `/p/:slug` with
   no auth.
5. **Save**.

The tenant-admin view of the same page is at `/workspace/:slug`.

## Public pages + embed origins

A public page at `/p/:slug` is served with a
`Content-Security-Policy` header derived from the tenant's
`allowed_embed_domains`. Two cases:

- **Unset** — CSP is `frame-ancestors 'self'`, so the page only
  renders when embedded on the same origin.
- **Set to a comma- or space-separated domain list** — those
  domains are appended as allowed frame ancestors.

That lets you embed a public FastYoke page inside your own
customer portal without relaxing the CSP globally. See
[Embed Config](/docs/forms/builder#themes--branding) for the
frontend contract (`GET /api/v1/public/embed-config?slug=...`
returns the resolved CSP for use in the embedding shell).

> **Tenant-aware embeds**
>
> Tenants with a `custom_domain` set can serve public pages on
>   their own domain. The platform-admin shell's tenant detail page
>   is where you assign it. Once set, `/p/:slug` on the custom
>   domain resolves to the same page as `/p/:slug` on the platform
>   domain — same row, different host.

## Inheritance (enterprise)

Pages participate in the inheritance engine. Master-tenant
pages propagate to subsidiary tenants as read-only mirrors; the
designer renders an inherited page's blocks locked and hides the
Save button. Edits happen in the master — changes sync on the next
inheritance run.

## Page per entity (optional)

`/admin/pages/entity/:entityName` is a convenience URL that opens
the designer on a page keyed to an entity kind. This is the
entry-point most admins use: design the detail view for a
`vehicle`, a `delivery`, a `work_order`. The same page shape
backs the public `/p/...` URL.

## Related

- [SDK Reference](/docs/sdk/reference) — registering custom block
  types via extensions.
- [Entities](/docs/entities) — how annotations upgrade the
  `entity_list` block's configurability.
- [Workflows](/docs/workflows) — FSM schemas the `job_list` and
  `action_button` blocks reference.
