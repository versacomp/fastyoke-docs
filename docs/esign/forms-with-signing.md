---
title: Forms with signing
summary: Configure a form for inline single-signer ceremony or multi-signer send-for-signature; the signing plan, the submit hook, and the admin dashboard.
order: 2
---

# Forms with signing

This page covers the two form-driven signing modes: a single signer
who signs inline at submit, and a configured plan of up to three
signers who sign in order — mixing inline capture with emailed
invitations.

## Single-signer inline ceremony

Open a form in the Forms Builder and set `requires_esign_ceremony`
on the form definition. When a respondent submits the form, the
platform parks the submission as `awaiting_signature`, mints a
short-lived ceremony token, renders the PDF for the signer to review,
and presents a signature pad. On signing, the platform seals the
envelope and the submission moves to `signed`.

The signer's email is taken from the first email field on the form.
The consent disclosure shown above the signature pad is the
platform-managed version 1 disclosure — its hash, the signer's IP,
the user-agent, and the agreed-at timestamp all enter the audit log.

## Multi-signer signing plan

For two or three signers, open the **Signing** tab in the Forms
Builder. Add one slot per signer. Each slot carries:

- **Role label** — a human-readable name (`minor`, `parent1`,
  `parent2`, `vendor`).
- **Capture mode** — `inline` (the signer signs as part of submit)
  or `send` (the signer receives an emailed link).
- **Signature field** — the document field this signer's signature
  stamps into.
- **Email source** — either a form-field reference (e.g., the
  `parent2_email` field) or a static address.
- **Order tier** — an integer; signers in the same tier sign in
  parallel, signers in higher tiers can't start until the prior
  tier finishes.
- **`required_when`** — a JSONLogic expression evaluated against
  the form submission. If it evaluates falsy, the slot is skipped.
  Example for a parent / minor consent form:
  `{ "!=": [ { "var": "sole_guardian" }, true ] }` — parent2 is
  only required when the form's `sole_guardian` field is not
  checked.

## Order tiers and conditional signers

Order tiers determine when each signer can act. Tier 0 signers
sign first; tier 1 starts only after every required tier 0 signer
has signed. Conditional skipping happens at tier-entry: if a slot's
`required_when` evaluates falsy at that moment, the slot is skipped
and the next tier proceeds.

The envelope finalizes once — the call to `finalize_envelope` runs
on the last required signature. Skipped signers don't block
finalization.

## What the submit hook does

Submitting a form with a multi-signer plan creates an `in_progress`
envelope and a row in `esign_signers` per slot. Inline signers in
tier 0 are presented with the ceremony page immediately. The
remaining signers receive invitation emails with portal links.

When a signer signs, the platform appends to the audit log,
overlays that signer's signature into the document at the assigned
field, and either advances to the next tier or finalizes the
envelope.

## Sending invitations and voiding envelopes

The **Envelopes** dashboard in the admin shell lists every envelope
across forms-with-signing and uploaded-PDF flows. From the detail
view you can:

- Resend a signer's invitation email.
- Void an in-progress envelope with a reason. Voiding a completed
  envelope is not supported — the sealed PDF and Certificate of
  Completion are immutable.

## API reference

The submit hook fires `POST /esign/seal` automatically for the
single-signer inline path. The explicit endpoint is available for
advanced cases — for example, an external system that generates
the document outside the form flow.

```http
POST /esign/seal
Content-Type: application/json
Authorization: Bearer <admin JWT>

{
  "envelope_id": "env_…",
  "signers": [ { "email": "…", "ip": "…", "user_agent": "…", "agreed_at": "2026-06-19T12:00:00Z" } ]
}
```

Response: the envelope's `verification_id`, public verify URL, and
the Certificate of Completion attachment id.

To resend a multi-signer invitation:

```http
POST /esign/envelopes/:id/signers/:signer_id/send
Authorization: Bearer <admin JWT>
```

Returns 204 on success; the platform records the resend in the
audit log.
