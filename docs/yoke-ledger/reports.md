---
title: Reports
summary: Trial Balance, Profit & Loss, and Balance Sheet — three financial statements computed on demand from posted entries, with reconciliation identities the platform preserves at write time.
order: 5
---

# Reports

Yoke Ledger ships three financial statements. Each one
is computed on demand from your posted entries — there's
no overnight batch, no warehouse, no separate read store.

## Trial Balance

Every account with its total debits and total credits over
the report window. For the report to make sense, **debits equal credits** across the whole report — that's the daily
integrity check that says "the books are consistent."

If your Trial Balance ever doesn't balance, the cause is
upstream of the report — the platform refuses to write an
unbalanced entry, so a non-balancing Trial Balance points
at a bug in the platform (or a hand-edit of the database
that bypassed the API).

## Profit & Loss

Revenue minus Expenses over a period. P&L answers "did we
make money this period?"

The period boundary is a query parameter. The default
period is the current calendar month. You can pass any
start / end date pair and get the same shape back.

## Balance Sheet

A point-in-time snapshot. **Assets = Liabilities + Equity**
— the fundamental accounting identity. The platform preserves
this identity at write time (by enforcing the per-entry
balance invariant); the report just adds up the surviving
posted entries up to the as-of date.

The default as-of date is today. Pass `?as_of=YYYY-MM-DD`
to roll the snapshot back to any prior day.

## Performance

Reports compute on demand from the posted-entry table.
For tenants with very large entry counts, read latency
scales with entry count, not user count. This is fine
for the kind of volumes most tenants run; if your books
ever outgrow that, the deferred list below names the
features that would address it.

## What's not in reports today

- **Cash Flow Statement** as a fourth statement.
- **Comparative periods** (this period vs. last period
  side-by-side).
- **Exports** — CSV, XLSX, PDF.
- **Per-department slicing** — filter the statements by
  a department tag.

## See also

- [Journal entries](/docs/yoke-ledger/journal-entries) —
  the input to every report.
- [Chart of accounts](/docs/yoke-ledger/chart-of-accounts)
  — the row shape of the Trial Balance.
- [API reference](/docs/yoke-ledger/api-reference) — the
  `/reports/*` endpoints.
