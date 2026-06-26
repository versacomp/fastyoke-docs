---
title: Map an existing Chart of Accounts
summary: Three migration shapes for replacing the starter CoA with your client's books — replace, merge, or augment — and the deactivate-not-delete rule.
order: 1
---

# Map an existing Chart of Accounts

When you install Yoke Ledger, the platform seeds a
starter Chart of Accounts (AR / AP / Cash / Revenue /
COGS / Owner's Equity / etc.). For a brand-new business,
that's enough. For a client migrating from QuickBooks /
Xero / a spreadsheet, you'll want to bring their existing
CoA over.

## The starter CoA

The seeded accounts at install time:

- 1000 Cash (Asset)
- 1200 Accounts Receivable (Asset)
- 1500 Fixed Assets (Asset)
- 2000 Accounts Payable (Liability)
- 2500 Loans Payable (Liability)
- 3000 Owner's Equity (Equity)
- 4000 Revenue (Revenue)
- 5000 Cost of Goods Sold (Expense)
- 6000 Operating Expenses (Expense)

For the field-level reference (account types, code
formats, validation rules), see [Yoke Ledger Chart of
Accounts](/docs/yoke-ledger/chart-of-accounts).

## Three migration shapes

### Replace

The cleanest shape when the client's CoA is well-
maintained and the starter accounts don't match what
they want.

1. Open Chart of Accounts → bulk delete the starter
   accounts (none have historical entries yet).
2. Import the client's CoA via bulk-ops importer.
3. Run a Trial Balance — it should be empty until you
   start posting.

### Merge

The right shape when the client's CoA partially overlaps
the starter set.

1. Rename the starter accounts to match the client's
   code/name (e.g., starter `1000 Cash` → `1010 Cash —
   Operating`).
2. Add the client's additional accounts that aren't
   seeded.
3. Run a Trial Balance — confirm structure.

### Augment

When the starter CoA is fine but the client has additional
accounts they need (e.g., multiple revenue streams).

1. Leave starter accounts in place.
2. Add the client's additional accounts.

## The CoA importer format

A CSV with columns:

| Column | Required | Notes |
| --- | --- | --- |
| `account_code` | yes | Numeric, 4-6 digits, unique. |
| `account_name` | yes | Free text. |
| `account_type` | yes | One of: `asset`, `liability`, `equity`, `revenue`, `expense`. |
| `parent_code` | no | For sub-accounts. |
| `is_active` | no | Default `true`. |

## The deactivate-not-delete rule

**Yoke Ledger refuses to delete an account that has
historical journal entries.** Deactivate, do not delete.
This is feature, not bug — the platform protects the
audit trail by preventing referential integrity loss.

For accounts the client doesn't use anymore:

1. Set `is_active = false`.
2. Deactivated accounts:
   - Disappear from transaction pickers (no new entries
     can post to them).
   - Stay on historical reports (Trial Balance, GL
     detail).

Deactivation is reversible. Deletion (on accounts with no
history) is final.

## Confirming the migration

1. Run the Trial Balance in the client's old system on
   their cutover date.
2. Import opening balances into Yoke Ledger as a single
   journal entry dated the cutover date.
3. Run the Trial Balance in Yoke Ledger.
4. The two must match exactly. If they don't, find the
   delta before posting any new transactions.
