---
title: Rate cards
summary: Per-end-customer markup schedules. The __default__ sentinel applies to every end customer without an override; absent the default, wholesale + 0% is in effect. Replace is atomic.
order: 2
---

# Rate cards

A **rate card** is a per-end-customer markup schedule
that sits on top of the platform's wholesale price.
You manage your tenant's rate cards from
**partner-portal → Billing → Rate cards**.

## The `__default__` sentinel

The rate card map is keyed by end-customer slug, with
one reserved key: `__default__`. The default rate card
applies to every end customer who doesn't have their
own override. If no default exists, **wholesale + 0%**
is in effect — your retail equals your wholesale and
your margin is zero.

You can set a default and then override specific
end customers. The lookup is: per-customer first,
`__default__` second, zero-markup last.

## Markup mechanics

The rate card multiplies the platform's wholesale
invoice line items by the markup factor. The end
customer never sees the wholesale number on their
invoice — the platform stores both for the
reconciliation report only.

A 25% markup on a $100 wholesale line item produces a
$125 retail line item and a $25 margin on the
reconciliation row. Wholesale never leaks to the
end-customer-facing invoice.

## Editing

- `GET /rate-card` returns the active rate card map.
- `PUT /rate-card` replaces it.

The replace is **atomic** — partial updates require sending the full map. Sending a `PUT` with only
`{ "acme-co": ... }` and omitting `__default__` will
remove the default rate card, not preserve it. Read
first, mutate locally, send the merged result.

## What lives on a rate card today

- Per-end-customer markup percentage.
- Optional override label (used on the end-customer
  invoice line in place of the platform's wholesale
  line text).

## What's not on a rate card today

- **Time-windowed rates** — different markup during
  a promotional window.
- **Conditional markup** tied to specific products
  or metered events.
- **Free trial periods** per end customer.

## See also

- [Statements & reconciliation](/docs/reseller-billing/statements-and-reconciliation)
  — where the markup decisions surface as margin.
- [Invoices](/docs/reseller-billing/invoices) — where
  the markup is locked in.
- [API reference](/docs/reseller-billing/api-reference)
  — the `/rate-card` endpoints.
