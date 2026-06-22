---
title: Certification
summary: How completion certificates are issued — rides on the M14 e-sign trust model. No new platform endpoint. Self-referential by design.
order: 8
---

# Certification

When a learner's capstone Tenant Yoke is accepted, the
training operator issues a **completion certificate** —
a sealed PDF with a public verify URL — through the
platform's own e-sign envelope flow. Anyone can verify the
cert without an account; the trust model is the same
ed25519 seal that learners exercised in [Lab M14](/docs/training/labs/M14-ledger-esign).

## The trust model in one sentence

The cert is a sealed PDF served by the platform. The
**public verify endpoint** at `/api/v1/public/esign/verify`
proves the platform serialized this exact document at this
exact time. Hiding the verify endpoint behind auth would
destroy the trust model — the cert is meaningful precisely
because anyone can check.

## Who can issue a certificate

The **training operator** — whoever runs your training
program — accepts capstones and issues certs. There is no
separate "certification authority" subsystem; the cert is
an e-sign envelope like any other signed document, and the
permission to send it is the standard `esign:admin` scope.

## How to request a certificate (learner side)

1. Submit your capstone via the
   [Capstone](/docs/training/capstone) submission flow.
2. A reviewer evaluates against the rubric. If accepted,
   the reviewer issues the certificate as the next step
   — you don't need to request it separately.
3. You'll receive an e-sign envelope email titled
   *FastYoke training — Certificate of Completion*.
   Sign it in the signer portal.
4. The platform serves the sealed PDF (your cert + the
   Certificate-of-Completion page). The Certificate page
   carries the public verify URL — share it freely.

## How to issue a certificate (reviewer side)

The starter repo at
[`training-starter/certification/`](https://github.com/versacomp/fastyoke2/tree/main/training-starter/certification)
ships a print-styled HTML template + a render script.
After accepting a capstone:

1. **Render the PDF.**

   ```bash
   cd training-starter/certification

   RECIPIENT_NAME="Learner Full Name" \
   CAPSTONE_TITLE="Their capstone Yoke title" \
   REVIEWER_NAME="Your name" \
   ISSUED_DATE="$(date -u +%Y-%m-%d)" \
   CERT_ID="cert_$(openssl rand -hex 4)" \
     bash render.sh
   ```

   The script produces `certificate.pdf` next to the
   template.
2. **Upload as an e-sign envelope.** **Admin → E-Sign →
   New envelope.** Title: *FastYoke training — Certificate
   of Completion*. Add the learner as the sole signer with
   their preferred email. Send.
3. **Learner counter-signs.** Standard signer-portal flow.
4. **Done.** The sealed copy the platform serves IS the
   certificate. Its public verify URL is the trust artifact.

## How to verify a certificate

Anyone — a future employer, a peer, a stranger — can verify
a cert without an account:

```bash
curl -i \
  "https://www.fastyoke.io/api/v1/public/esign/verify?hash=$CERT_HASH"
```

The response carries the envelope id, sealed-at timestamp,
signer chain, and the Certificate-of-Completion render URL.
No auth header is required; that's the entire point.

## Why no auto-grader

The training-track [README](/docs/training/quizzes) explains
why quizzes have no auto-grader, and the same reasoning
applies here: an LLM-graded capstone drifts toward LLM-
producible answers, and the integration value isn't reducible
to a pattern match. Reviewers are humans.

## What the certificate carries

- The recipient's name.
- The capstone Yoke's title.
- The reviewer's name and the issue date.
- A unique certificate id.
- The platform's ed25519 seal over the bytes of the PDF.
- A Certificate-of-Completion page appended by the e-sign
  subsystem with the public verify URL.

The seal is the trust artifact — every other field is just
human-readable metadata that maps to what the seal covers.

## Revocation

If a cert needs to be revoked (rare — but possible if a
capstone is later found to violate the honor system), the
training operator voids the originating e-sign envelope via
the standard envelope lifecycle. The verify endpoint will
report the envelope as voided. There is no separate
cert-revocation subsystem.

## See also

- [Capstone](/docs/training/capstone) — the integration
  project the cert attests to.
- [Lab M14 — Ledger & E-sign](/docs/training/labs/M14-ledger-esign)
  — where learners exercise the verify endpoint.
- [Lecture M14](/docs/training/lectures/M14-ledger-esign) —
  the e-sign trust model in narrative form.
