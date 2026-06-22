---
title: Quiz M11 — Webhooks, notifications & messaging
summary: 10 multiple-choice questions on HMAC verification, delivery-id dedup, the DLQ, rotate-secret dual window, and the SSRF guard.
order: 12
---

# Quiz M11 — Webhooks, notifications & messaging

Pairs with [Lecture M11](/docs/training/lectures/M11-webhooks)
and [Lab M11](/docs/training/labs/M11-webhooks).

**Q1.** Why is the SSRF guard "non-counting" toward auto-pause?

a) It refuses traffic that isn't a real failure of the receiver
b) For performance
c) Legacy
d) It does count, actually

<details>
<summary>Answer + why</summary>

**a)** Refusals are a separate counter so legitimate
receivers aren't penalized for the platform's own
refusals.

</details>

**Q2.** What's the rotate-secret dual window for?

a) Performance
b) Both previous and new secrets verify during a grace period
c) Backwards compat
d) Cross-region replication

<details>
<summary>Answer + why</summary>

**b)** Lets you roll the receiver to the new secret
before the old one stops working.

</details>

**Q3.** What event sources emit outbound webhooks?

a) Only job transitions
b) Job transitions, form submissions, billing events
c) Only billing
d) Anything in the event log

<details>
<summary>Answer + why</summary>

**b)** Multiple sources flow through the same outbound
dispatcher.

</details>

**Q4.** What's `X-FastYoke-Delivery` for?

a) Routing
b) Receiver-side deduplication
c) Rate-limit metadata
d) Auth

<details>
<summary>Answer + why</summary>

**b)** Same id reappears on DLQ replay; receiver dedups.

</details>

**Q5.** Why hash the raw bytes for HMAC verification?

a) Faster
b) Re-serializing reorders keys or whitespace and breaks the hash
c) Required by RFC
d) For cross-platform support

<details>
<summary>Answer + why</summary>

**b)** Different serializers produce different bytes — the
hash is over what the platform sent.

</details>

**Q6.** What's the SSRF guard's refusal list?

a) Non-HTTPS, RFC-1918, link-local, IPv6 ULA
b) Only non-HTTPS
c) Only private IPs
d) Anything outside an allowlist

<details>
<summary>Answer + why</summary>

**a)** Layered — protocol + private ranges +
unroutable.

</details>

**Q7.** What does `replay-dlq` redeliver?

a) New events generated since the failure
b) The original failed deliveries, with the same delivery ids
c) A merged batch
d) Compressed event diffs

<details>
<summary>Answer + why</summary>

**b)** Original deliveries, original ids — your receiver
dedups.

</details>

**Q8.** Notifications vs messaging — which is which?

a) Notifications = conversational; messaging = one-way
b) Notifications = one-way bell-style; messaging = conversational
c) Aliases
d) Notifications are paid; messaging is free

<details>
<summary>Answer + why</summary>

**b)** Different consumption models; both ride the same
event spine.

</details>

**Q9.** A webhook subscription pauses automatically after…

a) 1 consecutive failure
b) 10 consecutive failures (excluding SSRF refusals)
c) The first 4xx
d) 100 failures of any kind

<details>
<summary>Answer + why</summary>

**b)** Real failures count toward auto-pause; refusals
don't.

</details>

**Q10.** Can a receiver target a private (RFC-1918) IP for "internal" use?

a) Yes — internal IPs are fine
b) No — the SSRF guard refuses dispatch
c) Only via a special endpoint
d) Only on Enterprise

<details>
<summary>Answer + why</summary>

**b)** Private IPs are always refused, no override.

</details>
