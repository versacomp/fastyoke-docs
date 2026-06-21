---
title: Posting rules
summary: Declarative mappings from a source event to a journal entry shape. The Posting Rule Designer authors them; idempotent execution dedupes per source event.
order: 4
---

# Posting rules

A **posting rule** is a declarative mapping from a source
event (an `invoice` created in CRM Sales, a `sale` rung up
on POS, a `payment` recorded in the dealer payment ledger)
to a journal entry shape. Rules are how other Yokes get
their activity onto the books without anyone hand-typing
journal entries.

## The rule shape

Each rule names:

- A **source kind** — which event the rule fires on.
- A **debit account code** — which account gets debited.
- A **credit account code** — which account gets credited.
- An **amount derivation** — usually "the source event's
  amount field," sometimes a constant or a formula over
  source fields.

The platform stores rules per tenant. Two tenants can have
different rules for the same source kind.

## Sources

The set of source kinds available to your tenant is exposed
at `GET /gl/posting-rules/sources`. The default shipped
sources include `invoice` (CRM Sales, dealer line items) and
expand as Yokes that emit posting sources are installed.

## The default seeded rule

Install seeds **one rule**: `invoice → AR / Revenue`. Any
Yoke that emits an `invoice` source posts a journal entry
debiting AR and crediting Revenue automatically.

You can:

- **Edit** the rule (change accounts, change amount
  derivation).
- **Disable** the rule (it stops firing without being
  removed).
- The default rule cannot be deleted entirely — disable
  instead. (This prevents an accidental "rule's gone, why
  isn't my CRM posting?" moment.)

## Idempotency

When a source event re-fires (a webhook retry, an FSM event
that fires twice, a replay during recovery), **the same source event creates one posted entry, not two**. The
platform deduplicates by the source event's id within the
tenant — a rule that fired against `invoice id=42` will
not fire against `invoice id=42` a second time, ever.

## The Posting Rule Designer

The Posting Rule Designer is the in-product UI for
authoring rules. It sits in **Settings → Yoke Ledger →
Posting rules** and follows the same Builder-surface
pattern as the rest of the platform's authoring tools.
See [Workflows](/docs/workflows) for the Builder pattern
the Designer mirrors.

Designer features that customers see today:

- Pick a source from the live `/sources` list.
- Pick debit and credit accounts from your CoA.
- Configure the amount derivation as a field from the
  source event, a constant, or a formula.
- Save, preview against a recent source event, enable /
  disable.

## What's not in posting rules today

- **Conditional rules** — fire only when amount > X, only
  when a tag is set, only on weekdays.
- **Multi-leg rules** — split the amount across more than
  two accounts.
- **Deferral schedules** — recognize revenue over a
  period.
- **Rule simulation / dry-run** — preview a month's worth
  of rule firings before enabling.

## See also

- [Journal entries](/docs/yoke-ledger/journal-entries) —
  what a rule produces.
- [Chart of accounts](/docs/yoke-ledger/chart-of-accounts)
  — what the rule's accounts reference.
- [Workflows](/docs/workflows) — the Builder-surface
  pattern the Designer follows.
- [API reference](/docs/yoke-ledger/api-reference) — the
  `/gl/posting-rules` endpoints.
