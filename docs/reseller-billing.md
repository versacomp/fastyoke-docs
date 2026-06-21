---
title: Reseller billing
summary: Channel-partner billing — rate cards, white-label invoices, statements with margin reporting, and Vendara-Connect-driven settlement. For Channel Partners reselling FastYoke to their own end customers.
order: 1
---

# Reseller billing

Channel Partners reselling FastYoke to their own end
customers get a separate billing surface. This section
documents it: rate cards, white-label invoices,
statements, the invoice lifecycle, and the settlement
handoff to Vendara.

If you're a **direct customer** paying FastYoke for your
own tenants, see [Billing](/docs/billing) instead. This
page is for the **partner** side of the relationship.

## Where this surface lives

The reseller billing screens live in the **partner-portal**
shell — a separate URL the partner authenticates into
using the hosting auth-exchange flow. Inside the portal
you see your own end-customer roster, the rate cards
attached to each one, live statements, the invoice list,
and the reconciliation report.

## The model

Three terms carry their weight across every page in this
section:

- **Wholesale** — what FastYoke charges *you*, the
  partner, for each end-customer tenant (tier base plus
  metered usage). You see the wholesale numbers in the
  partner portal; your end customers never do.
- **Retail** — what *you*, the partner, charge your end
  customer. You set this via a rate card.
- **Margin** — retail minus wholesale. The
  reconciliation report breaks margin out per period
  bucket.

## End-to-end tour

1. **Set a default rate card.** Any new end customer
   inherits the markup on `__default__` until you give
   them their own override.
2. **Override per end customer** where the negotiated
   terms differ. See [Rate cards](/docs/reseller-billing/rate-cards).
3. **Configure your billing profile** so invoices carry
   your brand instead of "FastYoke." See
   [White-label branding](/docs/reseller-billing/white-label).
4. **Let usage accrue.** The live statement reflects
   what would be invoiced if the period closed right
   now.
5. **Generate an invoice** at period close — the
   platform freezes the snapshot.
6. **Issue the invoice** to Vendara. Vendara collects
   from the end customer; your payout follows.
7. **Reconcile** per period for margin reporting.

## What's not in reseller billing today

- **Multi-tier discount schedules** with volume
  breakpoints.
- **Per-end-customer invoice cadence** (quarterly for
  some customers, monthly for others).
- **Multi-currency** invoice support.
- **Reseller-of-resellers** — sub-partner hierarchies.
- **Auto-applied promo codes** on retail amounts.

## See also

- [Rate cards](/docs/reseller-billing/rate-cards)
- [Statements & reconciliation](/docs/reseller-billing/statements-and-reconciliation)
- [Invoices](/docs/reseller-billing/invoices)
- [White-label branding](/docs/reseller-billing/white-label)
- [API reference](/docs/reseller-billing/api-reference)
- [Billing](/docs/billing) — direct-customer billing.
- A future Channel Partner Portal section will cover the
  deals pipeline, provisioning, and training tracks.
