---
title: Yoke Ledger
summary: A free double-entry General Ledger Yoke — Chart of Accounts, journal entries, declarative posting rules, and three financial statements your other Yokes can post into.
order: 1
---

# Yoke Ledger

Yoke Ledger is a double-entry General Ledger that other Yokes
post into. Install it from the [Marketplace](/docs/marketplace) and your tenant gets
a working Chart of Accounts, an FSM-driven journal-entry
workflow, a default posting rule, and three financial
statements — Trial Balance, Profit & Loss, Balance Sheet.

## Gating

The listing is `fastyoke.yoke-ledger`. It is **free** and
available to every paid tier from Solo and above. Install
auto-grants the entitlement — there is no Stripe checkout
step.

## What install seeds

When you install the Yoke, the platform writes three things
into your tenant on the first request:

- **The `gl_journal_entry` FSM schema** — the workflow
  definition that drives the `draft → posted ↔ reversed`
  lifecycle.
- **A starter Chart of Accounts** — AR, AP, Cash, Revenue,
  COGS, and a small set of standard accounts. You edit them
  to match your books from there.
- **A default posting rule** — `invoice → AR / Revenue`.
  Any Yoke that emits an `invoice` source event posts a
  journal entry crediting Revenue and debiting AR
  automatically.

## The never-delete-the-books guarantee

If you uninstall Yoke Ledger from the Marketplace, the
platform uninstall revokes the entitlement but retains all GL data — your accounts, your entries, your posting rules,
your reports stay exactly where they were. Reinstall and
they're all there. The books cannot be lost by a misclick.

## What's not in Yoke Ledger today

- **AR and AP modules** beyond the default invoice rule.
- **Period close** and fiscal-year boundary workflows.
- **Multi-currency** — USD only.
- **Reconciliation against bank statements** — match
  posted entries against an imported statement.
- **Real-time consolidated reporting** across tenants in
  the same org.

## See also

- [Chart of accounts](/docs/yoke-ledger/chart-of-accounts) —
  the five account types, codes, and the editor surface.
- [Journal entries](/docs/yoke-ledger/journal-entries) —
  lifecycle, balance invariant, immutability.
- [Posting rules](/docs/yoke-ledger/posting-rules) — the
  declarative rule shape and the sources that fire them.
- [Reports](/docs/yoke-ledger/reports) — the three financial
  statements.
- [API reference](/docs/yoke-ledger/api-reference) — the
  REST surface.
