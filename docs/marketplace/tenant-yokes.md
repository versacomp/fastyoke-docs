---
title: Tenant Yokes (custom)
summary: Author your own listings on a single tenant. Verticals bundle related listings together. Clone-from-source uses an existing listing as a starting point. Yoke Studio is the authoring UI.
order: 4
---

# Tenant Yokes (custom)

A **Tenant Yoke** is a custom listing **you** author on a
single tenant. Your Tenant Yoke can be installed only in
your tenant; it never enters the public catalogue.

The surface lives at **Settings → Marketplace → Tenant
Yokes** in the admin shell.

## REST surface

- `POST /api/v1/tenant/marketplace/tenant-yokes` — Create a Tenant Yoke.
- `PUT /api/v1/tenant/marketplace/tenant-yokes/:slug` — Update a Tenant Yoke definition.
- `DELETE /api/v1/tenant/marketplace/tenant-yokes/:slug` — Delete a Tenant Yoke.
- `GET /api/v1/tenant/marketplace/tenant-yokes/:slug/history` — Read the Tenant Yoke's change history.

Each Tenant Yoke is identified by a tenant-scoped `slug`.
Two Tenant Yokes in different tenants can share a slug —
they're not part of the public catalogue's namespace.

## Verticals

A **vertical** is a curated bundle of listings that go
together for a specific industry — Auto Dealer, Patient
Flow, Convention, etc. Installing a vertical kicks off
install on each listing in the bundle.

- `GET /verticals` lists every vertical the platform
  ships.
- `GET /verticals/:slug` returns one vertical's
  composition.

Verticals are platform-curated; you don't author your
own. They're the "start-from-bundle" companion to the
"start-from-single-Yoke" install flow.

## Clone from an existing listing

You can spin up a Tenant Yoke pre-populated from any
public listing's structure using
`POST /clone/:source_slug`. The clone copies the source's
shape (FSM schemas, page layouts, default seed) into a
new Tenant Yoke owned by your tenant.

This is the natural starting point when you want a
CRM-Suite-shaped layout, an Auto-Dealer-shaped layout,
etc., as a base for your own customizations. The clone
is independent — updates to the source listing don't
propagate to your clone.

## Yoke Studio

The Tenant Yoke definition is JSON. You can edit it
directly via the API, but the in-product authoring
experience is [Yoke Studio](/docs/apps/studio) — drafts,
autosave, schema authoring, and the visual layout
builder. Studio writes against the same REST surface
documented above.

## Primitives

Tenant Yokes draw from a read-only catalogue of
building blocks — workflows, entities, forms, pages.
`GET /primitives` lists them; `GET /primitives/:slug`
returns one. Authoring tooling (Yoke Studio, the
manual JSON editor) renders the same list.

## What's not in Tenant Yokes today

- **Publishing a Tenant Yoke to the public catalogue.**
- **Cross-tenant Tenant Yoke sharing** — clone an exact
  Tenant Yoke into another tenant.
- **Yoke versioning beyond the history endpoint** —
  named versions, tags, rollback to a prior version.

## See also

- [Marketplace overview](/docs/marketplace).
- [Yoke Studio](/docs/apps/studio) — the authoring UI.
- [API reference](/docs/marketplace/api-reference) for the
  request/response shapes.
