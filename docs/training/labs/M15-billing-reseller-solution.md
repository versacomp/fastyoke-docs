---
title: Lab M15 — Solution
summary: Answer key for the M15 billing lab — exact wallet/usage curls, the dual-threshold contract, reseller rate-card note, Strategic Partner consent semantics.
order: 31
---

# Lab M15 — Solution

> **Reveal warning.** This page is the **answer key** for
> [Lab M15 — Billing, reseller & Strategic Partner](/docs/training/labs/M15-billing-reseller).
> Try each step on your own first; the friction is where the
> learning lives.

## Step 2 — Usage rollup response shape

```json
{
  "period": "2026-06",
  "tier_base_cents": 0,
  "metered": [
    { "resource": "transitions", "count": 1247, "cost_cents": 124 },
    { "resource": "storage_bytes", "count": 8420000000, "cost_cents": 42 },
    { "resource": "outbound_email", "count": 18, "cost_cents": 18 }
  ],
  "total_cents": 184
}
```

## Step 3 — Wallet response

```json
{
  "balance_cents": 1500,
  "low_balance_alerted_at": null,
  "suspended_at": null
}
```

After Step 5 trips the alert, `low_balance_alerted_at` carries
a UTC timestamp.

## The dual-threshold contract

- **$10 low-balance alert.** The platform emails the org
  admin when the wallet drops below $10. Idempotent per UTC
  day — a second sweep on the same day won't re-send.
- **$0 auto-suspend.** When the wallet reaches $0, the
  tenant is suspended (read-only). Crediting the wallet
  triggers `unsuspend_on_credit`.
- **Metered-only mode.** Org admins can opt into
  metered-only billing (no tier base), which exempts the
  org from wallet auto-suspend — the org pays metered costs
  invoiced at month-end.

## Reseller note

Resellers configure a **rate card** with a `__default__`
sentinel rate plus per-customer overrides. Their end
customers never see the wholesale cost. The white-label
billing profile controls what name appears on the invoice.

## Strategic Partner consent

Strategic Partner consent is **per-request**, not
per-session. Every partner-initiated action on behalf of an
end customer records a consent row at the moment the
action runs. There is no "always allow" — that would
defeat the audit chain.

## Back to the lab

Return to [Lab M15 — Billing, reseller & Strategic Partner](/docs/training/labs/M15-billing-reseller).
