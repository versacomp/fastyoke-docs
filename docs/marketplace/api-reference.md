---
title: API reference
summary: REST surface for the Marketplace — listings, install, uninstall, checkout, Tenant Yokes, verticals, clone, draft, primitives.
order: 5
---

# API reference

Every endpoint is tenant-scoped. The platform derives the
`tenant_id` from the JWT; you never pass it in the body
or in a path segment.

## Auth

Tenant JWT in `Authorization: Bearer …`. Endpoints return
`401` if the JWT is missing or invalid.

## Catalogue

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/api/v1/tenant/marketplace/listings` | List every public listing in the catalogue. |

## Install lifecycle

| Method | Path | Purpose |
| --- | --- | --- |
| POST | `/api/v1/tenant/marketplace/listings/:id/install` | Install a listing. Free → grants entitlement + seeds. Paid → 402 if tier-blocked; otherwise hands off to checkout. |
| POST | `/api/v1/tenant/marketplace/listings/:id/uninstall` | Revoke entitlement. Data retained. |
| POST | `/api/v1/tenant/marketplace/listings/:id/checkout` | Open a Stripe Checkout session for a paid listing. Returns a session URL. |

## Tenant Yokes

| Method | Path | Purpose |
| --- | --- | --- |
| POST   | `/api/v1/tenant/marketplace/tenant-yokes` | Create a Tenant Yoke. |
| PUT    | `/api/v1/tenant/marketplace/tenant-yokes/:slug` | Update a Tenant Yoke definition. |
| DELETE | `/api/v1/tenant/marketplace/tenant-yokes/:slug` | Delete a Tenant Yoke. |
| GET    | `/api/v1/tenant/marketplace/tenant-yokes/:slug/history` | Tenant Yoke change history. |
| POST   | `/api/v1/tenant/marketplace/yoke-draft` | Save a draft Tenant Yoke (Studio's autosave target). |

## Verticals

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/api/v1/tenant/marketplace/verticals` | List curated bundles. |
| GET | `/api/v1/tenant/marketplace/verticals/:slug` | One vertical's composition. |

## Clone-from-source

| Method | Path | Purpose |
| --- | --- | --- |
| POST | `/api/v1/tenant/marketplace/clone/:source_slug` | Create a Tenant Yoke pre-populated from a public listing. |

## Primitives

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/api/v1/tenant/marketplace/primitives` | List building-block primitives Tenant Yokes can draw from. |
| GET | `/api/v1/tenant/marketplace/primitives/:slug` | One primitive's definition. |

## Error codes

| Code | Meaning | Returned by |
| --- | --- | --- |
| `tier_required` | The tenant's tier is below the listing's `tier_required`. Body carries the listing slug and the required tier. | `POST /listings/:id/install` |
| `not_entitled` | The caller doesn't hold the listing's entitlement. | Listing-private routes guarded by the entitlement gate. |

Both are 402 responses.

## See also

- [Marketplace overview](/docs/marketplace).
- [Install & uninstall](/docs/marketplace/install-and-uninstall).
- [Listings & pricing](/docs/marketplace/listings-and-pricing).
- [Tenant Yokes](/docs/marketplace/tenant-yokes).
