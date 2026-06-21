---
title: Statements & reconciliation
summary: Statements are live per-end-customer roll-ups; reconciliation buckets break wholesale / retail / margin / margin % per period. Voided invoices are excluded.
order: 3
---

# Statements & reconciliation

## Statements

`GET /statements` returns live per-end-customer
roll-ups. Each statement shows:

- The period boundary (current period by default).
- Line items at **retail** prices (after rate-card
  markup).
- The running total for the period.

Statements are **never frozen.** Every request
recomputes against the current rate card and the
underlying utility ledger. If you change a rate card
mid-period, the live statement reflects the new markup
on the next request.

Use statements for:

- Real-time "where am I this period?" checks.
- Investigating a dispute against a generated invoice
  by comparing the live recompute to the frozen snapshot.

## Reconciliation

`GET /reconciliation` returns **period buckets** per
scope (the partner aggregate plus one per end customer).
Each bucket carries four numbers:

| Column | Meaning |
| --- | --- |
| `wholesale` | What FastYoke charged the partner. |
| `retail` | What the partner charged the end customer. |
| `margin` | `retail - wholesale`. |
| `margin %` | `margin / retail` as a decimal. |

**voided invoices are excluded from the reconciliation buckets.** A voided invoice's line items don't roll into either the wholesale or the retail column. The margin row treats it as if the invoice never existed.

This matters when you void-and-reissue: the voided
row drops out and the new row replaces it cleanly. The
reconciliation report stays the source of truth for
"actual realized margin this period."

## Empty buckets

Scopes with no activity in a period get **explicit empty rows** in the response — zeros across all four columns — rather than being silently omitted.
This is so the partner sees explicit coverage rather
than wondering whether a missing row means "no activity"
or "endpoint dropped it."

## What's not in reconciliation today

- **Weekly cadence** — periods are monthly.
- **Day-of-week bucket alignment** — periods align to
  calendar month boundaries.
- **Exportable CSV** of the reconciliation rows.

## See also

- [Rate cards](/docs/reseller-billing/rate-cards) — the
  upstream of every margin number.
- [Invoices](/docs/reseller-billing/invoices) — what
  gets reconciled.
- [API reference](/docs/reseller-billing/api-reference)
  — the `/statements` and `/reconciliation` endpoints.
