---
title: Lecture M15 — Billing, reseller billing & Strategic Partner
summary: The prepaid wallet's dual-threshold contract, the __default__ sentinel in rate cards, and why Strategic Partner consent is per-request.
order: 16
---

# Lecture M15 — Billing, reseller billing & Strategic Partner

## The mental model

The platform meters everything it does and charges from a
**prepaid wallet**. Resellers layer their own pricing on
top via **rate cards** and **white-label billing
profiles**. Strategic Partners (frontline support /
implementation consultancies) act on tenants' behalf —
gated by **per-request consent** rather than persistent
delegation.

## Key concepts

- **Prepaid wallet.** Each org has one. Metered usage
  draws down; top-up via Stripe (prod) or the sandbox
  test-credit endpoint.
- **Dual-threshold contract.** **$10 low-balance alert**
  emails the org admin (idempotent per UTC day). **$0
  auto-suspend** flips the tenant to read-only.
- **Metered-only mode.** Org-level opt-in: no tier base,
  metered costs invoiced at month-end. Exempt from
  wallet auto-suspend.
- **Reseller rate cards.** A card per reseller with a
  `__default__` sentinel rate plus per-customer overrides.
  End customers never see the wholesale cost.
- **White-label billing profile.** Controls what
  organization name appears on the invoice. Configurable
  per-period.
- **Strategic Partner consent.** Per-request, not
  per-session. Every partner-initiated action on behalf
  of an end customer writes a consent row at the moment
  it runs. No "always allow" — that would defeat the
  audit chain.
- **Egress metering.** Email ($0.001/msg), webhook
  notifier (flat), extension-bundle bytes (bucketed).
  All bills to the wallet.

## Common pitfalls

- **Crediting prod via the sandbox endpoint.** It only
  works in sandbox. Prod tops up through Stripe.
- **Authoring per-customer overrides without the
  `__default__` sentinel.** New customers won't have a
  card and the resolver will refuse. The sentinel is the
  fallback.
- **Caching Strategic Partner consent.** Don't. The
  audit value is the per-request record. Caching defeats
  it.

## Where to go next

- Lab: [M15 — Billing & reseller](/docs/training/labs/M15-billing-reseller).
- Reference: [/docs/billing](/docs/billing),
  [/docs/reseller-billing](/docs/reseller-billing),
  [/partners/inetko](/partners/inetko).
