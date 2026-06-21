---
title: Chart of accounts
summary: The five account types, the code convention, the editor surface, and the deactivate-not-delete rule that preserves your historical entries.
order: 2
---

# Chart of accounts

Your Chart of Accounts (CoA) is the list of accounts you
post journal entries against. The platform ships a starter
CoA on install; you edit it from there.

## The five account types

Every account belongs to exactly one type. The platform
validates the type case-sensitively against these five
exact strings:

- **Asset** — what the business owns. AR, Cash, Inventory.
- **Liability** — what the business owes. AP, accrued
  expenses.
- **Equity** — owners' stake. Retained earnings,
  contributed capital.
- **Revenue** — income from operations.
- **Expense** — costs of operations. COGS, payroll, rent.

Wrong-case strings (`asset`, `LIABILITY`) are rejected by
the create / patch handler — the platform won't paper over
a typo.

## Account codes

Each account has a short stable identifier called a **code**
(e.g., `1000` Cash, `1100` AR, `4000` Revenue). Codes are:

- **Tenant-scoped** — every tenant picks its own
  numbering.
- **Unique within the tenant** — the platform rejects a
  second account using the same code.
- **Stable** — code is the addressing key journal entries
  use; rename via PATCH instead of delete + recreate so
  your existing entries stay reachable.

The platform doesn't enforce a numbering scheme — you can
use 1000/2000/3000/4000/5000 blocks, you can use your
prior bookkeeping codes, or any convention your accountant
prefers.

## The editor surface

The CoA editor lives at **Settings → Yoke Ledger → Chart
of accounts** in the admin shell. From there you can:

- Create an account inline (code + name + type).
- PATCH an account to rename it or recategorize.
- Deactivate an account so it disappears from the New
  Entry picker without disturbing historical entries.

There is no destructive delete. Deactivating an account
keeps every historical entry pointing at the same code —
posted entries still reconcile, the Trial Balance still
balances, and a reactivation restores the account in one
click.

## What's not in the CoA today

- **Sub-accounts** and account hierarchies (parent/child).
- **Custom account-type validators** beyond the five
  standard types.
- **Per-tenant default-CoA templates** that an org can
  apply when provisioning a new tenant.

## See also

- [Journal entries](/docs/yoke-ledger/journal-entries) —
  what an entry against these accounts looks like.
- [Posting rules](/docs/yoke-ledger/posting-rules) — how
  other Yokes target your accounts by code.
- [API reference](/docs/yoke-ledger/api-reference) — the
  `/gl/accounts` endpoints.
