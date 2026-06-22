---
title: Quiz M4 — Forms
summary: 10 multiple-choice questions on zod-mirrored schemas, anti-abuse defenses, public-link submissions, and form-to-job wiring.
order: 5
---

# Quiz M4 — Forms

Pairs with [Lecture M4](/docs/training/lectures/M4-forms)
and [Lab M4](/docs/training/labs/M4-forms).

**Q1.** Why are form schemas zod-mirrored rather than raw JSON Schema?

a) JSON Schema is slower
b) zod gives TypeScript inference + matches the Rust serde shape
c) Legacy reasons
d) Smaller bundle size

<details>
<summary>Answer + why</summary>

**b)** zod gives client-side validation + inferred types;
the discipline is that both sides validate the same shape.

</details>

**Q2.** What does a public-link form use to defeat abuse?

a) Captcha only
b) Layered: per-IP rate limit → captcha → soft block
c) Auth header
d) IP whitelist

<details>
<summary>Answer + why</summary>

**b)** Anti-abuse is layered — each layer kicks in at a
higher abuse threshold.

</details>

**Q3.** How does a public-link form authenticate the submitter?

a) Anonymous session
b) WorkOS guest
c) It doesn't — public means public
d) A short-lived JWT

<details>
<summary>Answer + why</summary>

**c)** If the submitter needs auth, the form lives behind
a session. Adding a token query param to a "public" link
defeats the model.

</details>

**Q4.** What happens if a frontend field is added but not the Rust struct?

a) The field is silently dropped
b) The submission rejects with 400
c) The platform auto-adds it to the struct
d) It validates but is never readable

<details>
<summary>Answer + why</summary>

**b)** Mirrored schemas. Drift on either side rejects on
the next round trip.

</details>

**Q5.** What types of file-upload validation does the platform run?

a) Extension only
b) Extension, size, and MIME
c) MIME only
d) None — by design

<details>
<summary>Answer + why</summary>

**b)** Three layers, because each catches a different
spoofing pattern.

</details>

**Q6.** Can submitting a form start an FSM job in the same call?

a) Yes — the form payload becomes the job's initial entity row
b) Yes, but requires two API calls
c) No — separate ceremony
d) Only on Enterprise

<details>
<summary>Answer + why</summary>

**a)** Form-to-job wiring is first-class — one submission
creates the entity and starts the job atomically.

</details>

**Q7.** What happens when a public link crosses its anti-abuse threshold?

a) Returns 429
b) Silently drops new submissions
c) Issues a one-time captcha then a soft-block if abuse continues
d) Bans the IP forever

<details>
<summary>Answer + why</summary>

**c)** Friction escalates progressively — captcha first,
soft-block only after sustained abuse.

</details>

**Q8.** Where do uploaded files live?

a) Cross-tenant blob store
b) Tenant-scoped blob storage
c) S3 with public ACL
d) In the entity payload as base64

<details>
<summary>Answer + why</summary>

**b)** Tenant-scoped — the file-storage prime directive
mirrors the entity one.

</details>

**Q9.** What's the right response if your form's anti-abuse threshold trips on legitimate traffic?

a) Disable anti-abuse
b) Lower the threshold
c) Rotate the link or add an auth gate
d) Open a support ticket

<details>
<summary>Answer + why</summary>

**c)** Public means public. Legitimate high-volume traffic
needs a different mechanism than a public link.

</details>

**Q10.** What does the platform do with anti-abuse refusals' counter?

a) Counts them toward the per-form auto-pause
b) Counts them separately so legitimate traffic isn't penalized
c) Logs only
d) Ignores them

<details>
<summary>Answer + why</summary>

**b)** Refusals don't shut down a healthy form just because
the public spotted it.

</details>
