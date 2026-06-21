---
title: API reference
summary: REST surface for partner-portal billing — rate card, profile, statements, reconciliation, invoice lifecycle.
order: 6
---

# API reference

Every endpoint is partner-scoped. The platform derives
the `partner_id` from the JWT minted by the hosting
auth-exchange flow; you never pass it in the body or
in a path segment.

## Auth

Partner-portal JWT in `Authorization: Bearer …`.
Endpoints return `401` if the JWT is missing or
invalid, `403` if the caller lacks the partner-billing
permission.

## Rate card

- `GET  /api/v1/partner-portal/billing/rate-card` —
  read the active rate card map.
- `PUT  /api/v1/partner-portal/billing/rate-card` —
  replace the rate card map atomically.

## Billing profile (white-label)

- `GET  /api/v1/partner-portal/billing/profile` —
  read the active billing profile.
- `PUT  /api/v1/partner-portal/billing/profile` —
  update `item_name_template` and `item_description`.

## Statements

- `GET  /api/v1/partner-portal/billing/statements` —
  live per-end-customer roll-ups for the current
  period (or a query-param-supplied window).

## Reconciliation

- `GET  /api/v1/partner-portal/billing/reconciliation` —
  period buckets per scope with the four columns
  (`wholesale`, `retail`, `margin`, `margin %`).

## Invoices

- `GET  /api/v1/partner-portal/billing/invoices` —
  list frozen invoices.
- `POST /api/v1/partner-portal/billing/invoices/generate` —
  generate from the current live statement.
- `GET  /api/v1/partner-portal/billing/invoices/:id` —
  read one frozen invoice.
- `POST /api/v1/partner-portal/billing/invoices/:id/issue` —
  hand the invoice to Vendara.
- `POST /api/v1/partner-portal/billing/invoices/:id/void` —
  void a generated-but-not-issued invoice.

## Error codes

| Code | Meaning | Returned by |
| --- | --- | --- |
| `invoice_locked` | Attempted to mutate (edit, void) an issued invoice. | `POST /invoices/:id/void` against an issued invoice; any attempt to edit a frozen invoice's lines. |

## See also

- [Overview](/docs/reseller-billing).
- [Invoices](/docs/reseller-billing/invoices) — the
  lifecycle these endpoints drive.
- [Rate cards](/docs/reseller-billing/rate-cards) — the
  upstream of the markup.
