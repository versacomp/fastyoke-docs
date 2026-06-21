---
title: Billing
summary: How the platform charges you — tier subscription, metered usage, prepaid wallet, top-ups, low-balance alerts, and the Stripe portal. Numbers live on /pricing.
order: 1
---

# Billing

The platform charges two ways, in parallel:

- **Tier subscription** — a flat monthly base for the tier you
  bought (Solo / Pro / Team / Enterprise / Fleet).
- **Metered usage** — per-event charges for specific operations
  (job transitions, outbound webhooks, email egress, bundle
  bytes). Metered charges draw down a prepaid wallet at the
  organization level.

The two are independent: a Pro-tier org pays the Pro base
monthly **and** draws metered events from the wallet. Tier
prices and per-event rates live on [`/pricing`](/pricing) —
this section explains the mechanism, not the numbers.

## Where billing surfaces in the app

- **Admin → Settings → Billing & usage** (every tenant) —
  current-period charges, subscription status, tier, and the
  "Manage billing" link out to the Stripe Customer Portal.
- **Org → Wallet** (metered orgs only) — current balance, the
  Top Up button, alert / suspend status.
- **Stripe Customer Portal** — past invoices, payment-method
  changes, subscription cancel.

## Numbers live on `/pricing`

We don't repeat tier prices or per-event rates in docs.
Marketing's [`/pricing`](/pricing) page is the authoritative
source. If a number you see here ever drifts from `/pricing`,
trust `/pricing`.

## Resellers

If you're managing billing for downstream customers through
the channel-partner portal, your surface is different — rate
cards, statements, reseller invoices. That lives under
[`/partner-portal`](/partner-portal) and ships its own docs.
Everything on this page is for direct-customer billing.

## What's not in billing today

The following are explicit non-features. Plan accordingly.

- **Self-serve plan change in-product** — today, tier changes
  go through the Stripe Customer Portal.
- **Per-tenant wallet** — wallets are organization-scoped,
  not tenant-scoped.
- **Native multi-currency display** — USD only.
- **Granular department-level cost allocation** — there's one
  invoice per org per period.
- **In-app spend forecasting** — the dashboard shows what
  you've spent, not what you're projected to spend.

## See also

- [Tiers & metering](/docs/billing/tiers-and-metering) — what
  the platform charges for, and the metered-only billing mode.
- [Wallet & top-ups](/docs/billing/wallet-and-topups) — alert
  and suspend thresholds.
- [Invoices & portal](/docs/billing/invoices-and-portal) —
  Stripe Customer Portal, drafts vs finalized invoices,
  subscription statuses.
- [API reference](/docs/billing/api-reference) — the REST
  surface.
- [Yoke Ledger](/docs/yoke-ledger) — if you want platform
  spend on your own books, post a manual journal entry
  against the platform-billing invoice.
