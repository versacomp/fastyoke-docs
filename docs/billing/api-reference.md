---
title: API reference
summary: REST surface for the billing tab — subscription, usage, portal, addons, checkout, metering toggle, wallet, and the wallet top-up checkout.
order: 5
---

# API reference

Every endpoint is tenant-scoped. The platform derives the
`tenant_id` from the JWT; you never pass it in the body or in
a path segment.

## Auth

Tenant JWT in the `Authorization: Bearer …` header. Endpoints
return `401` if the JWT is missing or invalid, `403` if the
caller lacks the billing permission for their role.

## Subscription & usage

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/api/v1/tenant/billing/subscription` | Current Stripe subscription status and period boundary. |
| GET | `/api/v1/tenant/billing/usage` | Current-period roll-up: tier base + metered events + total. |

**The `unavailable` response shape.** Both `/subscription`
and `/usage` (and `/wallet`, see below) can return a body
shape indicating the surface is "unavailable" for this tenant
— typically a Solo-tier tenant with no subscription row yet,
or a tenant whose org hasn't been wired into the billing
back-end. The frontend renders an empty state in this case;
clients integrating with the API should handle the
`unavailable` body as a valid 200 response, not a failure.

## Portal

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/api/v1/tenant/billing/portal` | Returns a short-lived Stripe Customer Portal session URL. |

## Add-ons

| Method | Path | Purpose |
| --- | --- | --- |
| GET    | `/api/v1/tenant/billing/addons` | List the tenant's active paid and free add-ons. |
| DELETE | `/api/v1/tenant/billing/addons/:addon_key` | Disable a free add-on. (Paid add-ons cancel through the portal.) |

## Checkout

| Method | Path | Purpose |
| --- | --- | --- |
| POST | `/api/v1/tenant/billing/checkout` | Start a Stripe Checkout for a tier upgrade. Returns a session URL. |
| POST | `/api/v1/tenant/billing/checkout/addon` | Start a Stripe Checkout for a paid add-on. Returns a session URL. |

## Metering toggle

| Method | Path | Purpose |
| --- | --- | --- |
| POST | `/api/v1/tenant/billing/metering` | Toggle whether the tenant is opted into metered event capture. |

## Wallet

| Method | Path | Purpose |
| --- | --- | --- |
| GET  | `/api/v1/tenant/billing/wallet` | Current org wallet balance and last-alerted-at marker. |
| POST | `/api/v1/tenant/billing/wallet/topup-checkout` | Start a Stripe Checkout for a wallet top-up. Returns a session URL. |

The wallet endpoints answer for the org that owns the calling
tenant. A tenant that doesn't belong to a metered org gets
the `unavailable` shape, same as the subscription endpoints.

## See also

- [Overview](/docs/billing) — what surfaces in the app.
- [Wallet & top-ups](/docs/billing/wallet-and-topups) — the
  consumer of the wallet endpoints.
- [Invoices & portal](/docs/billing/invoices-and-portal) —
  the consumer of `/portal` and `/subscription`.
