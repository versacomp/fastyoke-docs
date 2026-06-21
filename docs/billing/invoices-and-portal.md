---
title: Invoices & portal
summary: The Stripe Customer Portal as the "manage billing" surface, invoice drafts vs finalized invoices, the subscription statuses you can see, and how to change tier today.
order: 4
---

# Invoices & portal

## The Stripe Customer Portal

The platform delegates payment-method changes, past-invoice
viewing, and subscription cancellation to the **Stripe
Customer Portal**.

From the admin shell:

1. Open **Settings → Billing & usage**.
2. Click **Manage billing** — the platform calls
   `GET /portal`, which returns a short-lived portal session
   URL.
3. The portal opens in a new tab; close it when you're done.

We do not deep-link to specific portal pages — Stripe owns
the navigation inside the portal.

## Invoice drafts vs finalized invoices

| Surface | What it shows |
| --- | --- |
| **Settings → Billing & usage** (in product) | The current period's **draft** — what your invoice would look like if the period closed now. Updates on a cadence. |
| **Stripe Customer Portal** (out of product) | **Finalized** invoices for past periods. Authoritative. |

Drafts are not invoices. Once the period closes, Stripe
finalizes the draft and the result lands in the portal as a
finalized invoice; the in-product draft for that period is
no longer visible.

## Subscription statuses

The platform surfaces the current Stripe subscription status
on the Billing & usage tab. The values you can see:

- **`active`** — the subscription is in good standing.
  Everything works.
- **`past_due`** — a recent charge failed. Stripe will retry
  on the schedule configured for the account; meanwhile the
  platform keeps the subscription live.
- **`canceled`** — the subscription has ended. Access to
  tier-gated features stops at period end.
- **`trialing`** — the subscription is inside a trial window.
  Behaves like `active` until the trial ends.

Other Stripe statuses (`incomplete`, `incomplete_expired`,
`unpaid`) can appear during edge cases and are surfaced
literally — there's no platform-side renaming.

## Changing tier today

Tier changes go through the **Stripe Customer Portal** today.
From the portal you can:

- Cancel the subscription (takes effect at period end).
- Update the payment method.

For an **upgrade** (Solo → Pro, Pro → Team, etc.), open
**Settings → Billing → Upgrade** in product — the upgrade
flow opens a fresh Stripe Checkout for the new tier.

A self-serve **in-product tier change wizard** is on the
deferred list.

## What's not in this surface today

- **In-product tier-change wizard** — handled via the
  upgrade flow and the portal.
- **In-product invoice download** — invoices live in the
  portal.
- **Scheduled subscription pause** — cancel-and-resubscribe
  is the only workflow today.

## See also

- [Wallet & top-ups](/docs/billing/wallet-and-topups) — for
  changing the payment method used to top up the wallet.
- [API reference](/docs/billing/api-reference) — the
  `/portal`, `/subscription`, and `/checkout` endpoints.
