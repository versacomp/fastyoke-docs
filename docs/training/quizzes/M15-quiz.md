---
title: Quiz M15 — Billing, reseller billing & Strategic Partner
summary: 10 multiple-choice questions on the prepaid wallet's dual threshold, the __default__ sentinel, Strategic Partner consent, and metered-only mode.
order: 16
---

# Quiz M15 — Billing, reseller billing & Strategic Partner

Pairs with [Lecture M15](/docs/training/lectures/M15-billing-reseller)
and [Lab M15](/docs/training/labs/M15-billing-reseller).

**Q1.** What's the $10 low-balance threshold for, vs the $0 suspend threshold?

a) $10 emails an alert; $0 suspends the tenant (read-only)
b) $10 suspends; $0 alerts
c) Aliases of the same threshold
d) Both trigger emails only

<details>
<summary>Answer + why</summary>

**a)** Alert-then-suspend gives the admin a window to top
up.

</details>

**Q2.** Why is the `__default__` sentinel in rate cards important?

a) Cosmetic name
b) Fallback rate when a customer has no explicit override
c) Marks the wholesale rate
d) Reserved keyword for refunds

<details>
<summary>Answer + why</summary>

**b)** Without it, new customers have no card and the
resolver refuses.

</details>

**Q3.** Why is Strategic Partner consent per-request, not per-session?

a) Performance
b) The audit value is the per-request record; "always allow" defeats it
c) Session expiry can drift
d) Bug

<details>
<summary>Answer + why</summary>

**b)** Each partner-initiated action records a consent row
at the moment it runs.

</details>

**Q4.** What does metered-only mode opt out of?

a) Wallet auto-suspend (tier base = 0; invoiced at month-end)
b) The tier base only
c) Both
d) Neither — metered-only still suspends

<details>
<summary>Answer + why</summary>

**a)** Metered-only orgs are exempt from auto-suspend and
invoiced at month-end.

</details>

**Q5.** Crediting prod via the sandbox `wallet/credit` endpoint…

a) Works in both envs
b) Only works in sandbox — prod tops up through Stripe
c) Requires admin override
d) Returns a free trial

<details>
<summary>Answer + why</summary>

**b)** Test-credit is sandbox-only. Stripe is the prod
top-up path.

</details>

**Q6.** What does a white-label billing profile control?

a) The Stripe key used
b) What org name appears on the invoice
c) The currency
d) Tax id

<details>
<summary>Answer + why</summary>

**b)** Per-period configurable. Resellers' end customers
never see "FastYoke."

</details>

**Q7.** Can you cache Strategic Partner consent for performance?

a) Yes
b) No — caching defeats the per-request audit value
c) Yes, with a 5-min TTL
d) Only on Enterprise

<details>
<summary>Answer + why</summary>

**b)** The audit value is the per-request record.

</details>

**Q8.** What does the low-balance alert's idempotency window look like?

a) Per call
b) Per UTC day — no re-send the same day
c) Per hour
d) Forever (sent once only)

<details>
<summary>Answer + why</summary>

**b)** Date-keyed idempotency. The sweep stamps
`low_balance_alerted_at`.

</details>

**Q9.** What restores a suspended tenant?

a) Manual admin action only
b) Crediting the wallet triggers `unsuspend_on_credit`
c) The next sweep
d) A support ticket

<details>
<summary>Answer + why</summary>

**b)** Self-healing — the unsuspend hook fires on credit.

</details>

**Q10.** What is egress metering charging for?

a) Inbound API calls
b) Email ($0.001/msg), outbound webhooks (flat), extension-bundle bytes (bucketed)
c) Disk usage
d) Compute time

<details>
<summary>Answer + why</summary>

**b)** Egress is what costs FastYoke money to deliver, so
it's metered separately.

</details>
