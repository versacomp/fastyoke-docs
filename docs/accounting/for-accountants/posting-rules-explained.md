---
title: Posting Rules explained
summary: What Posting Rules are in accounting terms — recurring journal entry templates with an audit trail — and how they survive scrutiny.
order: 2
---

# Posting Rules explained

## In your terms

A **Posting Rule** is a recurring journal entry template
that fires automatically when a business event happens
(sale, invoice, payment received, refund issued). It is
the structured-event automation accountants have wanted
from their software for 30 years.

For the technical reference (rule syntax, validation,
event sources), see [Yoke Ledger Posting
Rules](/docs/yoke-ledger/posting-rules).

## The audit story

Every rule firing writes an **immutable row** in the
event log. For each posted journal entry, you can prove:

- Which rule produced it.
- When it fired (UTC timestamp).
- What inputs the rule received.
- Who (or what) triggered the event.

This trail is what makes Posting Rules survive scrutiny.
An auditor asking "where did this Revenue entry come
from?" gets a chain back to the originating business
event with no holes. See [the audit-trail page](/docs/accounting/for-accountants/audit-trail).

## The default rule

When you install Yoke Ledger, one rule is seeded:

> **`invoice` event → debit AR, credit Revenue.**

Standard accrual-basis treatment. When the rule is wrong:

- **Cash-basis books** — disable the rule; create entries
  manually when customer pays.
- **Point-of-sale (customer pays immediately)** — change
  the debit side from AR to Cash.
- **Service vs goods split needed** — clone the rule per
  revenue category.

## Authoring a custom rule

1. Open Yoke Ledger → Posting Rules → New rule.
2. Pick the triggering event (e.g., `payment_received`,
   `expense_recorded`).
3. Define the debit + credit sides. The rule must
   produce a **balanced entry** — Yoke Ledger refuses to
   save an unbalanced rule.
4. Save.
5. **Test against historical events** before going live
   — the rule editor shows a preview of the entries
   that would have been produced over the last 90 days.

## The reversal mechanic

**Posted journal entries are immutable.** This is GAAP-
aligned and an auditor will expect it.

Yoke Ledger handles corrections through reversal:

1. Original entry stays posted.
2. The reversal is a **new entry** — same accounts,
   sides flipped. Both entries appear in the GL.
3. Optionally, the corrected entry posts after the
   reversal.

The audit chain shows the original, the reversal, and
the correction. Nothing was edited.

This is not a bug to work around. It's how
defensible books are kept.
