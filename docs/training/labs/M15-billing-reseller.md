---
title: Lab M15 — Billing, reseller & Strategic Partner
summary: Top up a sandbox tenant's prepaid wallet, inspect the metered usage rollup, then simulate usage and trip the low-balance alert.
order: 30
---

# Lab M15 — Billing, reseller & Strategic Partner

This lab pairs with **Module M15 — Billing & metering +
Reseller billing + Strategic Partner** in the
[syllabus](/docs/training/syllabus).

## What you'll do

Top up your sandbox tenant's prepaid wallet to a small
balance (just above the $10 low-balance threshold). Inspect
the metered-usage rollup via REST. Simulate metered usage
by firing a few FSM transitions, watch the wallet drain,
and observe the low-balance alert email when the balance
crosses $10.

## Before you begin

- A FastYoke **sandbox** tenant you're an admin of. (The
  test-credit endpoint only works in sandbox; in prod the
  wallet only credits via Stripe top-up.)
- A `fy_pat_` PAT with `billing:admin` scope (or admin
  session JWT).
- A job-bearing FSM you can drive transitions on (the
  `service_ticket` from M5/M6 works).
- `curl` and `jq`.
- About 40 minutes.

## Or use Postman

If you prefer a GUI, run the lab steps from Postman instead
of `curl`. Each request has a **Tests** tab with the same
checkpoint assertions the `curl` lab pins.

**Import once:** in Postman, choose **File → Import → Link**
and paste this URL:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

Click **Import**. Open the **Week 8 — Operations and capstone → M15 — Billing & wallet** folder, set the `FY_TOKEN` environment variable, then run each request in order.

The `curl` steps below remain the source of truth.

## Steps

1. **Credit the wallet (sandbox-only).**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"amount_cents":1500}' \
     https://www.fastyoke.io/api/v1/tenant/billing/wallet/credit
   ```

   **Checkpoint:** 200. The wallet balance is now $15.00,
   just above the $10 low-balance threshold.
2. **Inspect the metered usage rollup.**

   ```bash
   curl -i -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/billing/usage
   ```

   **Checkpoint:** 200 with a body that lists every
   metered resource (transitions, storage bytes, etc.) and
   the period-to-date totals.
3. **Get the wallet balance.**

   ```bash
   curl -i -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/billing/wallet
   ```

   **Checkpoint:** 200 with `{ "balance_cents": 1500 }`.
4. **Simulate metered usage.** Fire 5–10 FSM transitions
   on a job (each transition is a metered event). Use the
   M6 lab's transition curl in a `for` loop.

   **Checkpoint:** the wallet balance in Step 3 has
   dropped. Each transition costs a fraction of a cent;
   for the lab, the magnitude matters less than the
   direction.
5. **Trip the low-balance alert.** Either drive enough
   transitions to drop below $10, or re-credit the wallet
   with a smaller amount (e.g., `amount_cents=500`) and
   then drain. When the balance crosses $10, the platform
   sends a `LowBalance` email to the org admin.

   **Checkpoint:** the alert email arrives. The wallet's
   `low_balance_alerted_at` timestamp is set, so a second
   sweep on the same day won't re-send (idempotent).

## What you'll have at the end

A credited sandbox wallet, the metered-usage rollup, and
a tripped low-balance alert email.

## Stuck?

See [Lab M15 — Solution](/docs/training/labs/M15-billing-reseller-solution).

## Next

[Lab M16 — Capstone](/docs/training/labs/M16-capstone).
