---
title: Invoices
summary: Lifecycle is live → generated/frozen → issued → settled. Void applies only before issue. Vendara Connect collects from the end customer.
order: 4
---

# Invoices

A reseller invoice moves through a small, ordered set
of states. Each transition is explicit; no state
flows automatically except `issued → settled`, which
the platform polls Vendara for.

## Lifecycle states

| State | Meaning |
| --- | --- |
| `live` | Pre-generation. Visible only via `/statements` (live recompute). |
| `generated` / `frozen` | Snapshot captured by `POST /invoices/generate`. Immutable as a row. |
| `issued` | Handed to Vendara via `POST /invoices/:id/issue`. Vendara collects from the end customer. |
| `settled` | Vendara reported the payment closed. The platform polls and stamps the state. |
| `void` | Cancelled before issue. See "Void semantics" below. |

## Generate

`POST /invoices/generate` walks every end customer with
activity in the period and writes an immutable
`reseller_invoices` row for each. The snapshot
forever reflects the rate card and the utility ledger
**at generate time** — changing a rate card after
generation doesn't change the generated row.

The snapshot stores enough state forward-compat that
the platform can replay the right line item amounts
even if rate cards change later. This is the contract
that lets reconciliation reports stay deterministic
months after the fact.

## Issue

`POST /invoices/:id/issue` POSTs the invoice body to
your connected Vendara merchant account. **Issue locks the row** — once Vendara has the invoice, you cannot
edit a line on it through any API.

Vendara handles collection: it presents the invoice to
the end customer, accepts payment, and reports
settlement back. Your payout follows Vendara's
standard settlement cadence.

Vendara Connect setup (authorizing your merchant
account) lives in the **partner-portal payment-connection
surface**, not here.

## Void semantics

`POST /invoices/:id/void` voids a `generated` invoice
that has not yet been issued. The row's state flips to
`void`; the reconciliation report excludes it from
that period's buckets.

**You cannot void an issued invoice.** Once Vendara
has the row, corrections happen via a credit memo
against the next period — credit memos are on the
deferred list.

## REST surface

- `GET  /invoices` — list (frozen) invoices.
- `POST /invoices/generate` — generate from the
  current live statement.
- `GET  /invoices/:id` — fetch one frozen invoice.
- `POST /invoices/:id/issue` — hand to Vendara.
- `POST /invoices/:id/void` — void
  (generated-only).

## What's not in invoices today

- **Credit memos** against issued invoices.
- **Auto-issue** on a cadence at period close.
- **Invoice PDF preview** without generating.
- **Scheduled void** at a future date.

## See also

- [Rate cards](/docs/reseller-billing/rate-cards) —
  the markup that gets locked at generation.
- [Statements & reconciliation](/docs/reseller-billing/statements-and-reconciliation)
  — how voided invoices flow through the report.
- [White-label branding](/docs/reseller-billing/white-label)
  — what the end customer sees on the invoice.
- [API reference](/docs/reseller-billing/api-reference)
  — invoice endpoints and the `invoice_locked` error.
