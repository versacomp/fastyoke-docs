---
title: Close cycle
summary: Month, quarter, and year close on Yoke Ledger — the steps, the lock mechanism, and what the platform handles automatically.
order: 3
---

# Close cycle

## Month close — 5 steps

1. **Confirm all transactions posted.** Open Journal
   Entries → filter for "Draft" → review and post
   anything pending. No drafts should remain for the
   period being closed.
2. **Reconcile external accounts.** Bank, credit card,
   payment processor. Each external account's ending
   balance must match Yoke Ledger's cash subaccount for
   it.
3. **Trial Balance.** Reports → Trial Balance → as of
   month-end. Debits = Credits, to the cent. If not, find
   the delta before locking.
4. **P&L review.** Reports → Profit & Loss → period =
   the month being closed. Scan revenue and the major
   expense categories for unexpected items.
5. **Lock the period.** Reports → Period Lock → select
   the closed month. Yoke Ledger refuses new postings
   to a locked period without the admin override.

## Quarter close — additions

- **Depreciation entries** — book the period's
  depreciation expense + accumulated depreciation
  liability/contra-asset.
- **Accrual adjustments** — earned-but-unbilled revenue,
  expenses incurred but not yet invoiced.
- **Deferred revenue recognition** — recognize the
  portion of prepaid revenue earned this quarter.

These are journal entries you author manually (or via
custom Posting Rules if recurring); the close cycle is
the time to land them.

## Year close — additions

- **Closing income/expense accounts to Retained
  Earnings.** Yoke Ledger does this automatically when
  you mark the year as closed (Reports → Period Lock →
  Year close). No manual journal entries required.
- **Reset starts the new year clean.** Income and
  expense accounts open the new year at zero;
  Retained Earnings carries forward the cumulative
  profit/loss.

## What "locking" does

Once locked:

- New entries cannot post to a date in the locked
  period.
- The admin override can break the lock for late-
  arriving corrections (e.g., a $5 vendor invoice that
  arrived after close). The override leaves an audit
  row identifying who broke the lock, when, and why
  (mandatory free-text reason field).
- All historical reports for locked periods are
  read-only and reproducible.

For the audit-trail story behind these guarantees, see
[the audit trail](/docs/accounting/for-accountants/audit-trail).
