---
title: The signer experience
summary: What the signer sees and what they're agreeing to — the inline ceremony page, the emailed portal page, signature capture, and the decline path.
order: 4
---

# The signer experience

Signers reach FastYoke envelopes in two ways depending on the
configuration of the form or the envelope. Both flows share the
same evidence capture and the same signature pad.

## Ceremony page vs. portal page

- **Ceremony page** is loaded directly as part of submitting a form.
  The respondent fills out the form, hits submit, and the next
  screen is the signing ceremony — no email round-trip.
- **Portal page** is reached by an emailed invitation link. The
  invitation email contains a one-time URL signed against the
  envelope and the signer's slot. The signer clicks through and
  arrives at the portal page.

Both pages render the same content sections in the same order so
the signer's mental model is consistent.

## What's shown before signing

In order:

1. The fully rendered PDF preview — exactly the document that will
   be sealed. For forms-with-signing this is the form-generated
   PDF; for uploaded-PDF envelopes this is the source document
   with placed fields highlighted.
2. The consent disclosure — versioned text explaining what the
   signer is about to do, what the platform records, and how to
   exercise their right to withdraw. The disclosure version and
   its content hash enter the audit log.
3. The signer's email of record — pre-filled, displayed for
   confirmation. The signer can't edit it; if it's wrong they
   should decline and ask the admin to correct it.
4. **Optional access code** — if the envelope was configured with
   an access code, the signer enters it here. The platform
   verifies it against the stored hash before allowing the
   signature capture.

## How signature capture works

The signature pad is the shared `SignaturePad` component. Two
input modes:

- **Type** — the signer types their name and a stylized signature
  renders.
- **Draw** — the signer draws their signature with mouse or
  finger.

The captured signature is stored once as a PNG. If the signer is
assigned multiple signature fields in the document, the same PNG
stamps into every field.

After capture the signer reviews and clicks **Finish** to commit.
The platform records the signed-at timestamp, the signer's IP,
the user-agent, appends to the hash-chained audit log, and either
advances to the next signer (multi-signer plans) or finalizes the
envelope.

## Decline path

At any point before finishing, the signer can click **Decline**.
The portal shows a short reason field; on submit, the platform:

- Records the decline event in the audit log with the supplied
  reason, IP, and timestamp.
- Moves the envelope to `declined`.
- Notifies the admin via the dashboard.

A declined envelope cannot be revived; the admin starts fresh if
they still want signatures.

## What happens after signing

- **Single signer.** The envelope finalizes immediately. The
  sealed PDF and Certificate of Completion become available to
  the admin in the Envelopes dashboard.
- **Multi-signer, more signers to go.** The signer sees a
  confirmation screen. If the next signer's capture mode is
  `send`, their invitation email fires automatically. If it's
  `inline` (rare, but possible across tiers), the form flow
  picks it up at the next submit.
- **Last required signer.** Same confirmation screen, plus the
  envelope finalizes — last-signer overlays stamp into the
  document, audit chain is folded into the manifest, ed25519
  seal is computed, Certificate of Completion is rendered, and
  the sealed envelope is published. The admin gets a notice.

## What emails the signer can expect

- **Invitation email** (portal capture only). Contains the
  envelope's role label, the sender (the admin or company),
  and a one-time portal link. The link is signed and bound to
  the signer's slot and envelope.
- **Access-code email** (when an access code is configured).
  Sent separately, contains a short numeric code. Sending the
  link and the code separately is part of the email-control
  identity baseline.
- **Completion notice** — sent to every signer once the
  envelope finalizes. Contains a link to verify the sealed
  envelope through the public verify page.
