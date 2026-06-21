---
title: Marketplace
summary: The catalogue of installable Yokes — what a Yoke is, free vs paid, what install seeds into your tenant, and the retention contract that says uninstall does not delete your data.
order: 1
---

# Marketplace

A **Yoke** is a packaged FastYoke product — FSM schemas,
posting rules, app surfaces, and seed data — that you
install with one click. The Marketplace lives at
**Settings → Marketplace** in the admin shell.

## Two flavors

- **Free Yokes** — install auto-grants the entitlement, no
  Stripe checkout. Examples: [Yoke Ledger](/docs/yoke-ledger),
  Yoker (free tier).
- **Paid Yokes** — install opens a Stripe Checkout session;
  the entitlement is granted when payment completes. Example:
  [Compliance Yoke](/docs/compliance-yoke).

The marketing [/pricing](/pricing) page is the authoritative
source for dollar amounts. Marketplace docs describe the
*model* (Free, fixed monthly, metered), not the numbers.

## What install does

When you click Install on a listing card, the platform:

1. Checks your tenant's tier against the listing's
   `tier_required`. If your tier is below the requirement,
   install returns 402 with the listing slug and the required
   tier in the body — no entitlement is granted.
2. Grants the marketplace entitlement (free) or opens a
   Stripe Checkout session (paid). For paid listings the
   entitlement is granted on `checkout.session.completed`.
3. Seeds any FSM schemas the listing owns into your tenant.
   See [Install & uninstall](/docs/marketplace/install-and-uninstall)
   for the `owned_schemas` contract.
4. Seeds default rows (posting rules, accounts, sample data
   where applicable).

## What uninstall does

Uninstall revokes the marketplace entitlement and blocks
access to listing-private routes. It **does not delete your
data**. Every FSM schema the listing owns, every entity
record you created against those schemas, every posting rule
or seeded row — all of it stays in your tenant. Reinstall
and you pick up exactly where you left off.

This is the load-bearing retention contract. It is true for
every Yoke in the catalogue, free or paid.

## End-to-end tour

1. **Browse** the catalogue at Settings → Marketplace.
2. **Review** the listing card — flavor, tier requirement,
   what it seeds.
3. **Install.** Free Yokes flip on instantly; paid Yokes
   take you through Stripe Checkout.
4. **Use it** from the surface the Yoke ships
   (e.g., Settings → Yoke Ledger, Settings → Compliance
   Yoke).
5. **Uninstall** any time. Your data stays.

## What's not in the Marketplace today

The following are explicit non-features. Plan accordingly.

- **In-product self-publish** for Tenant Yokes beyond what
  Yoke Studio ships today.
- **Listing reviews / ratings** from other tenants.
- **Bundle discounts** at install time across multiple
  listings.
- **Private listings** shared by URL outside the public
  catalogue.
- **Listing search** and category filters in the admin
  shell catalogue view.

## See also

- [Install & uninstall](/docs/marketplace/install-and-uninstall) —
  the install decision tree and the retention contract in
  detail.
- [Listings & pricing](/docs/marketplace/listings-and-pricing)
  — the three pricing models and tier gating.
- [Tenant Yokes](/docs/marketplace/tenant-yokes) — author
  your own listing.
- [API reference](/docs/marketplace/api-reference) — the
  REST surface.
