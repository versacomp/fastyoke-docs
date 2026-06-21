---
title: Wallet & top-ups
summary: The org-level prepaid wallet, how top-up checkout works, the $10 low-balance alert, the $0 auto-suspend, and the documented exceptions for resellers and metered-only orgs.
order: 3
---

# Wallet & top-ups

The wallet is an **organization-level** prepaid USD balance.
Metered events draw it down; top-ups refill it. Each
organization has exactly one wallet; tenants inside an org
share the same balance.

## Topping up

From **Org → Wallet**, click **Top Up** and pick an amount.
The platform opens a Stripe Checkout session; on successful
payment the wallet is credited.

The credit is **idempotent** at the Stripe-session level.
The same `checkout.session.completed` event can never credit
the wallet twice — replays and retries are safe.

## Low-balance alert at $10

A background sweep runs hourly. Any organization whose wallet
balance falls below **$10** receives a `LowBalance` email and
is stamped as alerted for the current low-balance episode.

The platform sends one alert per episode, not one alert per
sweep. An "episode" ends when the balance climbs back above
$10 — a top-up clears the alert stamp, and a subsequent drop
below $10 starts a new episode (and a new alert).

## Auto-suspend at $0

The same sweep suspends any organization whose wallet balance
is at or below **$0**. A suspended org's tenants stop
accepting new metered events; in-flight events still draft to
the period draft so the unsuspend flow is clean.

A top-up that brings the balance above $0 unsuspends the org
automatically. There is no manual "unsuspend" button — pay
your wallet up and the suspend lifts on the next sweep tick
(or immediately, if the checkout webhook fires first).

## Documented exceptions

**Reseller orgs are alert-only.** Channel partners reselling
the platform to their downstream customers are never
auto-suspended on wallet balance — they continue to receive
low-balance alerts so they know to top up, but the sweep
skips the suspend pass for them. Resellers handle their
downstream customers' billing themselves.

**Metered-only orgs are exempt from auto-suspend.** Orgs
switched to the [metered-only billing
mode](/docs/billing/tiers-and-metering) are reconciled
out-of-band and therefore don't drive the wallet. The sweep
skips them for both alert and suspend passes.

## What's not in the wallet today

- **Auto-top-up** at a configurable threshold.
- **Multi-card failover** if the primary payment method
  fails.
- **Statement PDFs** emailed at period close (you read
  statements in the Stripe portal).

## See also

- [Tiers & metering](/docs/billing/tiers-and-metering) — how
  the wallet drains.
- [Invoices & portal](/docs/billing/invoices-and-portal) —
  the Stripe portal for changing payment methods.
- [API reference](/docs/billing/api-reference) — the wallet
  and top-up checkout endpoints.
