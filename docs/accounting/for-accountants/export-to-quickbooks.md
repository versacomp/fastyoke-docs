---
title: Export to QuickBooks
summary: Two-pass migration shape (CoA then journal entries) for moving a client's books to QuickBooks Online — generalizes to Xero, Sage, and Wave.
order: 5
---

# Export to QuickBooks

This page walks through QuickBooks Online (QBO)
specifically. The same two-pass shape applies to **Xero,
Sage, and Wave**; column names differ, link out at the
bottom.

## What QBO accepts

QuickBooks Online imports:

- **CoA** — CSV with the columns QBO's importer expects
  (account name, type, detail type, sub-account flag).
- **Journal Entries** — IIF format, or QBO's journal
  entry CSV (date, account code, debit, credit, memo).

QBO does not accept a single combined file. You need
two passes.

## The two-pass migration

### Pass 1 — Chart of Accounts

1. Yoke Ledger → Reports → Chart of Accounts → Export
   CSV.
2. Map the columns to QBO's importer format. Yoke
   Ledger's `account_type` maps to QBO's "Account Type"
   directly (asset/liability/equity/revenue/expense).
   QBO's "Detail Type" requires a second pick — usually
   inferable from the Yoke Ledger account name.
3. Import the CSV into QBO.
4. Confirm every account landed with the correct type
   and parent.

### Pass 2 — Journal entries

1. Yoke Ledger → Reports → General Ledger detail →
   period → Export CSV.
2. Verify the CoA from Pass 1 is in place. **If an
   account referenced in the JEs doesn't exist in QBO
   yet, the import fails.**
3. Map columns to QBO's journal entry CSV format.
4. Import in QBO. Run a Trial Balance — it should match
   Yoke Ledger's Trial Balance to the cent.

## What gets lost in translation

- **Posting Rules.** Yoke Ledger's automatic posting
  rules don't carry over. QBO has "Memorized
  Transactions" with different semantics. Recreate the
  rules as Memorized Transactions post-cutover if you
  want the automation.
- **Sealed PDF audit trail.** QBO doesn't have an
  equivalent. Keep the sealed PDF export of the
  pre-cutover GL — it's the auditor's evidence that the
  books were complete at the moment of migration.
- **Custom Yoke Ledger reports.** Built-in reports are
  available in QBO; custom report definitions don't
  port.

## Xero, Sage, Wave

Same two-pass shape. Column-level differences:

- **Xero** — uses "Account Code" (alphanumeric) instead
  of QBO's numeric. Type vocabulary differs slightly.
- **Sage** — "Nominal Code" instead of "Account Code".
  CSV-only import.
- **Wave** — minimal customization on import; usually
  accepts the QBO format with column renames.

For every target: validate the Trial Balance post-import
matches Yoke Ledger's pre-cutover Trial Balance. That's
the migration's exit criterion.
