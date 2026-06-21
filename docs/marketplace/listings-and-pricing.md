---
title: Listings & pricing
summary: Three pricing models — Free, fixed monthly, metered. Tier gating via tier_required. Free caps via free_cap. Downgrading doesn't auto-uninstall — the entitlement stays but listing routes start refusing.
order: 3
---

# Listings & pricing

## Three pricing models

Every listing carries exactly one pricing model:

- **`Free`** — no charge. Install auto-grants the
  entitlement; no Stripe step. Example: Yoke Ledger.
- **fixed monthly** — flat subscription per the listing's
  base price. Stripe drives the billing; you see the line
  item on your monthly invoice.
- **metered** — billed per usage event drawn from the
  tenant's metered-usage roll-up. The Yoker token add-on is
  a current example. See
  [Billing → Tiers & metering](/docs/billing/tiers-and-metering)
  for the metering plumbing.

Dollar amounts live on the marketing [/pricing](/pricing)
page. Marketplace docs describe the model; never the
number.

## Tier gating with `tier_required`

Every listing declares a `tier_required` value naming the
minimum tier that can install it. The platform checks
this at install time and returns 402 with the required
tier in the body if the tenant is below.

The decision is binary. There is no per-feature carve-out
in the catalogue — if a Yoke says `tier_required: Team`,
a Pro tenant can't install half of it. They upgrade or
they don't get it.

## Free caps with `free_cap`

Some free listings carry a `free_cap` — a usage ceiling
that, once hit, freezes new writes against the listing.
Reads keep working; existing rows are unaffected. To
restore writes:

- Upgrade the tenant tier (caps usually drop at higher
  tiers or are removed altogether), or
- Wait for the rolling window to advance (some caps are
  lifetime; some are time-windowed — read the specific
  Yoke's docs).

The Marketplace docs describe the **mechanism**; each
Yoke's docs page describes the **specific cap**.

## Marketplace add-ons

A paid add-on layered on top of a Yoke is treated as its
own listing for billing purposes: separate install,
separate entitlement, separate checkout. Install and
uninstall semantics are identical to primary listings.

Example: the Yoker LLM add-on layers on the Yoker base
listing. Installing the add-on doesn't change anything
about the base Yoker entitlement; it grants a second,
independent entitlement.

## What changes when tier changes

**Upgrade above `tier_required` for a previously blocked
listing** — the listing becomes installable on the next
install attempt. Previously blocked installs are not
auto-completed; you click Install again.

The case of downgrading below `tier_required` for an
installed listing is the subtle one worth documenting
clearly:

- The entitlement is **not** revoked.
- The platform **does not auto-uninstall** the listing or
  delete data.
- However, listing-private routes that gate on the tier
  check (not just the entitlement check) start returning
  402.

The net effect: an installed-then-downgraded tenant
keeps the entitlement on paper but loses access to the
guarded routes. Re-upgrading restores access without a
reinstall.

## What's not in pricing today

- **Annual prepay** — flat-pay 12 months at a discount.
- **Founder pricing** — locked-in early-adopter rates that
  survive future pricing changes.
- **Trial-with-install** — start a paid Yoke without a
  card for a fixed trial period.

## See also

- [Marketplace overview](/docs/marketplace).
- [Billing → Tiers & metering](/docs/billing/tiers-and-metering)
  — the metered-usage plumbing.
- [Install & uninstall](/docs/marketplace/install-and-uninstall).
