---
title: E-signatures
summary: Legally-meaningful e-signatures on forms or uploaded PDFs — three modes share one sealing engine, audit chain, and public verify endpoint.
order: 1
---

# E-signatures

FastYoke ships legally-meaningful e-signatures for forms and
pre-prepared PDFs. The platform captures the evidence chain a court
or compliance reviewer expects — signer identity, intent to sign,
consent disclosure, timestamps, IP, user-agent — and seals every
envelope with an ed25519 signature over a hash-chained audit log.
The public key is published; anyone can verify a sealed envelope
without calling FastYoke.

## Three modes, one engine

| Mode | When to use |
|------|-------------|
| **Inline ceremony** | A single signer reviews and signs a form's generated PDF at submit time. Best for consent forms, intake forms, single-party agreements. |
| **Send for signature** | One to three named signers in a defined order, mixing inline and emailed-link capture. Best for parent / minor consent, multi-party agreements where order matters. |
| **Uploaded PDF** | An admin uploads a prepared contract or document, drags signature and date fields onto it, then sends it for signature. Best for existing paperwork — contracts, NDAs, vendor agreements. |

All three modes share the same sealing engine, audit chain, and
Certificate of Completion. The difference is where the document
comes from and how signers reach it.

## When to use which

- **Pick Inline ceremony** when one person submits a form and signs
  it as part of the submit. The signing experience is part of the
  form flow; no email round-trip.
- **Pick Send for signature** when two or three parties must each
  sign in a defined order, the form is still the source of the
  document, and some signers need an emailed link instead of inline
  capture.
- **Pick Uploaded PDF** when the document already exists as a PDF
  and isn't a form-generated artifact. The admin drags signature
  fields onto the page coordinates that match the existing layout.

## Who reads which page

- **Tenant admins** — start with
  [Forms with signing](/docs/esign/forms-with-signing) for inline +
  multi-signer flows, or
  [Uploaded-PDF envelopes](/docs/esign/uploaded-pdf-envelopes) for
  contract-and-document workflows.
- **End-user signers** — see
  [The signer experience](/docs/esign/signer-experience) for what
  ceremony and portal pages look like and what happens after signing.
- **Compliance reviewers and counsel** — see
  [Trust and verify](/docs/esign/trust-and-verify) for the audit
  chain, the Certificate of Completion, the legal posture, and the
  public verify endpoint with offline-verification recipes.
