---
title: Journal entries
summary: Journal entries are FSM jobs. draft → posted ↔ reversed, immutable once posted, with a balance invariant the platform enforces at write time.
order: 3
---

# Journal entries

A journal entry is the unit of bookkeeping. Each entry has a
date, a description, and **two or more lines** — every line
is a debit against one account or a credit against another.

Under the hood, a journal entry is a workflow job on the
`gl_journal_entry` FSM schema. You don't have to know that
to use it; you do have to know the three lifecycle states
the schema models.

## Money

Every line carries an amount in **USD, two decimal places**.
The platform is single-currency today (see the deferred
list at the bottom of [the overview](/docs/yoke-ledger)).
Amounts are stored as a fixed-precision type — no floating
point rounding sneaks into your books.

## The balance invariant

Across an entry's lines, **sum of debits equals sum of credits**. The platform refuses to write an unbalanced
draft and refuses to post one — the validation runs on
both create and post, returning the same `unbalanced`
error code. There is no override.

This is the load-bearing double-entry property; everything
else (Trial Balance reconciles, Balance Sheet identity holds)
follows from it.

## The three lifecycle states

| State | What it is | What you can do |
| --- | --- | --- |
| `draft` | A pending entry, not yet on the books. | Edit lines, edit description, post, delete. Not visible in any statement. |
| `posted` | A committed entry. Live in every statement. | Read it. **Reverse** it. Cannot edit, cannot delete. |
| `reversed` | A posted entry whose effect has been reversed by a mirror entry. | Read it. The reversing entry is itself a separate posted entry — see "Reversing" below. |

## Posted entries are immutable

Once an entry is posted, its lines and amounts are
**immutable**. There is no edit-posted-entry path; any
attempt returns an `immutable` error. If you need to fix
a posted entry, you reverse it and post the corrected one.
This is the audit-trail contract: every dollar that ever
hit your books is reconstructable from the entry log.

## Reversing

Reversing a posted entry writes a **new, separate posted
entry** whose lines mirror the original — every debit
becomes a credit, every credit becomes a debit, same
amounts, same accounts. The original entry stays as it
was; the reversal is what reconciles its effect to zero.

After reversal the original entry's state is `reversed`
(the FSM transition fires). The reversing entry is in
`posted` (it's a live entry on the books). Both rows are
visible; together they net out to zero.

## What you can't do

- Edit a posted entry. Returns `immutable`.
- Delete a posted entry. The same `immutable` error.
- Post an unbalanced draft. Returns `unbalanced`.

## What's not in journal entries today

- **Draft templates** for repeat-pattern entries.
- **Recurring journal entries** (monthly amortization,
  payroll accrual).
- **Scheduled posting** at a future date.
- **Multi-line attachment uploads** (receipts, invoices)
  attached to the entry itself — today you attach via the
  entity-attachments surface.

## See also

- [Chart of accounts](/docs/yoke-ledger/chart-of-accounts) —
  what the lines reference.
- [Posting rules](/docs/yoke-ledger/posting-rules) — how
  source events become journal entries.
- [Reports](/docs/yoke-ledger/reports) — how posted entries
  roll up into statements.
- [API reference](/docs/yoke-ledger/api-reference) — the
  `/gl/entries` endpoints.
