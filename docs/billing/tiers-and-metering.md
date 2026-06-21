---
title: Tiers & metering
summary: The tier base + metered events the platform charges for, the metered-only org billing mode, and how the period rolls up into an invoice draft. Numbers live on /pricing.
order: 2
---

# Tiers & metering

## Tier base

Every paid tier carries a flat monthly base. The tier you
bought at checkout (Solo, Pro, Team, Enterprise, or Fleet)
sets the base; changing tier changes the base from the next
billing period. See [`/pricing`](/pricing) for the tier
matrix and the per-tier base price.

## Metered events

In addition to the tier base, the platform charges per-event
for specific operations. The kinds you can see on your
statement today:

- **Job transitions** — every FSM transition fired against
  one of your workflow jobs.
- **Outbound webhooks** — every successful delivery of a
  webhook subscription you configured.
- **Email egress** — every outbound platform email
  (notifications, mention emails, alerts).
- **Extension bundle bytes** — bandwidth served from your
  installed extensions, bucketed by byte count.

Per-event rates live on [`/pricing`](/pricing). Rates live on
`/pricing`, not in docs — anything we wrote here would drift
the moment marketing tunes a number.

## The metered-only billing mode

An organization can be switched to a **metered-only** mode by
the platform team. In that mode:

- The tier base is zeroed for billing purposes — you pay only
  for metered usage.
- The org is exempt from the prepaid-wallet auto-suspend (see
  [Wallet & top-ups](/docs/billing/wallet-and-topups)) because
  billing is reconciled out-of-band.
- The current-period draft on Settings → Billing & usage is
  stamped accordingly so the breakdown shows usage-only.

Metered-only is a per-org configuration handled by the
platform team — there is no self-serve toggle in product
today.

## Invoice drafts

At any moment during a billing period the platform maintains
a draft of what your invoice will look like at period end.
The draft rolls up the tier base and every metered event
attributable to the period. You can see the current draft on
**Settings → Billing & usage**; the finalized invoice is
delivered through the Stripe Customer Portal at period close.

## What's not in metering today

- **Per-tenant usage caps** — set a hard ceiling on metered
  events for a tenant.
- **Custom metered events** — meter a custom action defined
  in your workflow.
- **Real-time spend dashboards** — what's on Settings →
  Billing & usage updates on a cadence, not in real time.

## See also

- [Wallet & top-ups](/docs/billing/wallet-and-topups) — how
  the wallet drains and what happens when it's low.
- [Invoices & portal](/docs/billing/invoices-and-portal) —
  the finalized-invoice flow at period end.
- [API reference](/docs/billing/api-reference) — the
  endpoints behind these surfaces.
