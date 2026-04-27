# Apps

> Group pages under a shared slug + theme; the user-facing face of your tenant.

# Apps

An **app** is the top-level container your end-users see. It wraps
a set of [pages](/docs/pages) under a shared slug, assigns a
[theme](/docs/forms/builder#themes--branding), declares a
navigation menu, and (optionally) hosts an Advanced App Builder
configuration for enterprise delivery.

Every tenant has at least one app. Most tenants have one per product
line — `shipments`, `dispatch`, `billing` — each with its own URL
slug and brand.

## Anatomy

An app row in `apps` carries:

| Field | Purpose |
|---|---|
| `name` | Display name in the admin nav + header |
| `slug` | URL path: `/workspace/:slug` for admins, `/p/:slug` for public pages |
| `navigation_json` | Ordered array of `{label, page_slug}` entries |
| `theme_id` | Optional override; falls back to the tenant's default theme |
| `custom_css` | Per-app CSS layered after the theme — for fine-grained tweaks |
| `advanced_config_json` | Enterprise-only; see Advanced App Builder |

## Creating an app

From `/admin/apps`:

1. **New app**.
2. Give it a `name` and URL-safe `slug`.
3. Assign a theme (or leave blank to inherit).
4. Drop pages into the navigation in the order they should appear.
5. **Save**.

The app is immediately live at `/workspace/:slug` for authenticated
admins. Public pages inside the app resolve at `/p/:slug/:page_slug`
with the CSP derived from `allowed_embed_domains` (see
[Pages → public pages](/docs/pages#public-pages--embed-origins)).

## Navigation order

`navigation_json` is an ordered array. The nav editor lets you
drag to reorder:

```json
[
  { "label": "Overview", "page_slug": "overview" },
  { "label": "Shipments", "page_slug": "shipments" },
  { "label": "Reports", "page_slug": "reports" }
]
```

Each `page_slug` must reference an existing page in your tenant.
Missing references surface as orphan warnings in the Advanced
App Builder's Nav Health panel — the basic builder
warns inline with a red strike on the bad entry.

## Theme + custom CSS

Two layers, in order:

1. **Theme** — resolves to a set of `--brand-*` CSS custom properties
   (colors, fonts, border-radius, etc.). Applied to the app's root.
2. **`custom_css`** — raw CSS string the app owner can use for
   fine-grained overrides that don't warrant a full theme variant.
   Only the admin-authored value is trusted here; unlike Forms
   Builder's flagged custom CSS feature, apps' `custom_css`
   isn't sanitized — it's an admin-only surface, not a public
   one.

> **When to fork a theme vs bump custom_css**
>
> If the tweak would apply to more than one app, <em>fork the
>   theme</em> via <code>/admin/themes</code> — the fork becomes a
>   first-class theme you can attach to the other apps too. Only
>   reach for <code>custom_css</code> for the "last 5%" that's
>   genuinely app-specific.

## Advanced App Builder (enterprise)

Enterprise-tier tenants see an additional **Advanced** tab at
`/admin/apps/:id/advanced`. It holds two markdown artifacts
(`engineering_spec` + `feature_phases`) that describe the app's
intended SPA shape, a health panel that classifies every nav entry
as `ok` / `missing_page` / `orphaned_extension`, and delivery
actions:

- **Handoff zip** — download the artifacts + manifest stub for
  an SDK developer to pick up.
- **Generate extension** — produce an extension bundle from the
  artifacts. Two synth modes: deterministic **Template** (free)
  and **LLM-authored** (Claude Opus 4.7, ≈ $0.05 per call).

See the [Advanced App Builder](/docs/apps/advanced) page for the
full surface, including LLM setup + failure modes.

## Inheritance (enterprise)

Apps participate in the inheritance engine alongside
pages + FSM schemas. Master-tenant apps propagate as read-only
mirrors; the builder locks every surface on inherited rows.

## Related

- [Pages](/docs/pages) — the block-based views an app's navigation points at.
- [Workflows](/docs/workflows) — how FSM schemas get surfaced through app pages.
- [SDK → Block registration](/docs/sdk/reference) — extending the
  page block catalog via extensions mounted inside the app.
