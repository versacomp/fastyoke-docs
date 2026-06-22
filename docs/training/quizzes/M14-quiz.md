---
title: Quiz M14 — Yoke Ledger + E-signatures
summary: 10 multiple-choice questions on journal-entry immutability, the reversal contract, the seal's trust model, and posting-rule self-loops.
order: 15
---

# Quiz M14 — Yoke Ledger + E-signatures

Pairs with [Lecture M14](/docs/training/lectures/M14-ledger-esign)
and [Lab M14](/docs/training/labs/M14-ledger-esign).

**Q1.** Why are journal entries immutable once posted?

a) SQLite limitation
b) Audit trail integrity — same reason as the event log
c) Performance
d) Backwards compat

<details>
<summary>Answer + why</summary>

**b)** Mutation defeats the audit. Reversal appends a new
entry instead.

</details>

**Q2.** What does the platform's seal prove without a Certificate Authority?

a) Nothing — you need a CA for trust
b) The platform serialized this exact PDF at this exact time
c) Identity of the signer
d) Legal admissibility

<details>
<summary>Answer + why</summary>

**b)** Hash-chained ed25519 over the PDF + audit log.
Verifiable without a CA.

</details>

**Q3.** What's a self-loop in posting-rule terms?

a) Two rules with the same `rule_key`
b) A rule with `from == to` that fires without changing GL position (counter/retry pattern)
c) An infinite loop bug
d) A circular dependency between rules

<details>
<summary>Answer + why</summary>

**b)** Same FSM pattern from M6 applied to posting rules.

</details>

**Q4.** What does reversing a posted entry do?

a) Edits the original
b) Appends a new entry with mirrored sides
c) Deletes the original
d) Marks it inactive

<details>
<summary>Answer + why</summary>

**b)** History is never edited. The trial balance nets to
zero across the pair.

</details>

**Q5.** What does the public verify endpoint require?

a) An auth token
b) Nothing — no auth required (that's the trust model)
c) Tenant id
d) An invitation

<details>
<summary>Answer + why</summary>

**b)** The seal is meaningful precisely *because* anyone
can verify.

</details>

**Q6.** What happens if you re-export a sealed PDF from a different viewer?

a) Same seal verifies
b) Whitespace can reflow and break the seal
c) Auto-resigns
d) The seal is preserved by metadata

<details>
<summary>Answer + why</summary>

**b)** Always serve the sealed copy the platform stores.

</details>

**Q7.** What's a Posting Rule's `amount_path`?

a) An SQL expression
b) A JSON Pointer into the triggering event's payload
c) A constant cents value
d) A reference to another rule

<details>
<summary>Answer + why</summary>

**b)** JSON Pointer like `$.total` extracts the amount
from the event payload.

</details>

**Q8.** What happens if you try to PATCH a posted entry?

a) 200 with the patch applied
b) 409 — entry is immutable
c) 401
d) Auto-reverses

<details>
<summary>Answer + why</summary>

**b)** Immutable. Reverse and re-post is the contract.

</details>

**Q9.** What's hidden behind auth on the public verify endpoint?

a) Nothing — it's public
b) The full evidence chain
c) Verification result
d) The PDF download

<details>
<summary>Answer + why</summary>

**a)** Public means public. Hiding it defeats the trust
model.

</details>

**Q10.** Why does the e-sign subsystem mirror the GL subsystem's invariants?

a) Code reuse
b) Both prize immutability once published — and that's the audit value
c) Coincidence
d) Same author

<details>
<summary>Answer + why</summary>

**b)** Same architectural principle applied to different
domains.

</details>
