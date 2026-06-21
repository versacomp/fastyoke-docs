---
title: Install & uninstall
summary: Install grants entitlement and seeds schemas; uninstall revokes entitlement and keeps every row. Reinstall is a no-op-then-grant.
order: 2
---

# Install & uninstall

## Install decision tree

When you POST to `/api/v1/tenant/marketplace/listings/:id/install`,
the platform runs three checks in order:

1. **Tier check.** Compare the tenant's current tier
   against the listing's `tier_required`. If the tenant is
   below the requirement, return `402` with the listing
   slug and the required tier in the body. No entitlement
   is granted; no schemas are seeded.
2. **Pricing branch.**
   - **Free listing** → grant the marketplace entitlement,
     proceed to step 3.
   - **Paid listing** → return a Stripe Checkout session
     URL. The entitlement is granted asynchronously when
     `checkout.session.completed` fires from Stripe; the
     install endpoint itself does not flip the entitlement
     for paid listings.
3. **Seed.** Write any FSM schemas the listing owns into
   the tenant. Seed any default rows the listing declares
   (posting rules, accounts, sample data).

## The `owned_schemas` contract

Every listing declares the set of FSM schemas it owns. The
declaration is part of the listing definition; the
platform enforces:

- **The schemas listed in `owned_schemas` are seeded on
  install** (unless they're already present from a prior
  install / reinstall).
- Per platform invariant, **two listings cannot own the same schema**. The
  platform enforces this at listing registration; an
  attempt to register a colliding schema is rejected.
- **The schema stays for the lifetime of the tenant.**
  Even after uninstall, the schema row in
  `fsm_schemas` is preserved.

This is what makes the never-delete-the-data retention
contract reliable — schemas don't get yanked out from
under the rows that reference them.

## Uninstall semantics

`POST /api/v1/tenant/marketplace/listings/:id/uninstall`:

- Revokes the marketplace entitlement immediately.
- Removes the row from `marketplace_installs`.
- Marks listing-private routes 402-gated for subsequent
  requests.
- **Keeps** every FSM schema the listing owns.
- **Keeps** every entity record the tenant created against
  those schemas.
- **Keeps** every ancillary row seeded at install
  (posting rules, accounts).

There is no destructive-uninstall flag. The data stays.

## Reinstall

Reinstalling a previously uninstalled listing is a
two-step in practice:

1. The schemas already exist — the seeding step is a
   no-op. The platform doesn't re-write or re-seed
   anything.
2. The entitlement is **restored**. Whatever rows your
   tenant accumulated during the previous install
   re-appear immediately because they never left.

For paid listings, reinstall opens a new Checkout
session — the previous payment doesn't carry forward.
You're billed again from the moment Stripe confirms.

## What's not in install/uninstall today

- **Scheduled uninstall** (uninstall at a future date).
- **Multi-tenant batch install / uninstall** from the
  org-admin surface.
- **Install-with-config** — supplying custom seed
  parameters at install time (today seeds are fixed by
  the listing definition).

## See also

- [Marketplace overview](/docs/marketplace) — the wrapper
  story.
- [Listings & pricing](/docs/marketplace/listings-and-pricing)
  — the pricing models and tier gating.
- [API reference](/docs/marketplace/api-reference) — the
  install / uninstall / checkout endpoints.
