---
title: "Collect signatures"
summary: "Add a signature field and wire it into both your AcroForm and your Overlay template."
order: 7
tier: "201"
---

# Collect signatures

::callout{type="info" title="What you'll build"}
The `Patient Intake` form now captures a drawn signature from the
visitor and pipes the same image into both PDFs you've already
wired up: the AcroForm `consent.pdf` from tutorial 5 and the
Overlay `hipaa-acknowledgment.pdf` from tutorial 6. One capture,
two outputs, byte-deterministic on both sides.
::

::callout{type="note" title="Prerequisites"}
Finish [Place an Overlay template](/docs/forms/tutorials/201-overlay-template)
first. You need the `Patient Intake` form with both PDF templates
attached and the empty signature placeholder still sitting at the
bottom-left of the Overlay page.
::

A signature is the one field where the form schema and the PDF
templates have to agree end-to-end. The visitor draws once; the
captured image flows through the submit pipeline as an attachment,
and each template type consumes it differently — the AcroForm
renderer drops it into a real PDF signature widget, the Overlay
renderer composites it as an inline image inside the rectangle
you marked. Neither path re-prompts the visitor and neither path
reads a separate copy.

## 1. Add the signature field to the form

Open `Patient Intake` in the admin shell. The form already has
the `demographics` and `contact` pages from the earlier
tutorials. Add a third page named `consent` after them.

Drag a **signature** field onto the new page. Set the key to
`signature` and mark it **required**. Save the draft — the
field appears in the published schema as
`{ "key": "signature", "type": "signature", "required": true }`.

That's the entire form-side change. Both PDF templates now have
a signature payload key they can bind against.

## 2. Map the AcroForm signature widget

Open the **PDF templates** panel and click into the `consent.pdf`
row. The widget inventory from tutorial 5 lists `Patient Name`,
`Date of Birth`, `Email`, and `Today`. The PDF also carries one
more widget that you skipped earlier because there was nothing
to bind it to — a signature widget named `Signature`. It's a
real AcroForm signature field, not a text widget, so the
validator only accepts a signature-shaped binding.

The shape the renderer wants for a `Signature` widget is the
`__submitter_signature` system token. Pick **System token** in
the dropdown for the `Signature` row, then choose
`__submitter_signature`.

The full mapping now reads:

```json
{
  "entries": {
    "Patient Name": { "type": "form_field", "key": "full_name", "input_type": "text", "format_as_string": false },
    "Date of Birth": { "type": "form_field", "key": "dob", "input_type": "date", "format_as_string": false },
    "Email": { "type": "form_field", "key": "email", "input_type": "email", "format_as_string": false },
    "Today": { "type": "system_token", "token": "__submitted_at" },
    "Signature": { "type": "system_token", "token": "__submitter_signature" }
  }
}
```

The validator pairs `__submitter_signature` with the widget's
declared type on save — if the widget weren't a signature
widget the binding would come back with a `type_mismatch`
error against that row. The renderer reads the captured image
from the submission's attachment store and writes it into the
widget as a native PDF signature field, so a downstream
verifier sees a real signature, not a flattened bitmap.

## 3. Swap the Overlay placeholder for a signature region

Back in the **PDF templates** panel, open the
`hipaa-acknowledgment.pdf` row. In tutorial 6 you authored a
text region with id `r_signature_placeholder` bound to a
literal empty string — a deliberate stand-in. Edit that region
now: switch its **kind** from `text` to `signature`, and
switch its **binding** from the literal to
**Signature field** → `signature`.

The geometry stays exactly as you placed it. The region
serializes like this:

```json
{
  "id": "r_signature_placeholder",
  "page": 1,
  "x": 72.0,
  "y": 120.0,
  "w": 200.0,
  "h": 60.0,
  "kind": "signature",
  "binding": { "type": "signature_field", "key": "signature" },
  "font_size_pt": null
}
```

The save-time validator enforces the closed kind-to-binding
matrix: a `signature` region must bind to a `signature_field`,
and the `key` must point at a field whose form-schema type is
`signature`. Mismatches surface as `signature_field_required`,
`unknown_signature_field`, or `signature_field_wrong_type`
inline against the region. The other two regions you authored
in tutorial 6 — the patient-name text and the date text —
stay as they were.

::callout{type="info" title="One signature, two outputs"}
The same captured signature image flows to both renderers from
a single visitor capture. AcroForm consumes it through the
`__submitter_signature` system token and writes it as a native
PDF signature field on `consent.pdf`. Overlay consumes it
through the `signature_field` binding and composites it as an
inline image inside the rectangle on `hipaa-acknowledgment.pdf`.
The submit pipeline reads the attachment once and hands the
same bytes to both renderers.
::

## 4. Save and re-publish

Save both template panels. Then click **Publish** on the form
to cut a new definition version. As with the previous
tutorials, public links keep working across the republish —
the next visitor session loads the version that has the
signature field plus both updated mappings.

## Verify it worked

Open the public URL of `Patient Intake` in an incognito
window. Fill in the demographics and contact pages, then on
the new `consent` page draw a signature in the signature pad
and submit.

The submit response still carries the two `pdf_download_url`s
from before. Open the `consent.pdf` URL: the same four fields
fill as in tutorial 5, and the `Signature` widget now shows
your drawn signature inside its rectangle. Open the
`hipaa-acknowledgment.pdf` URL: the patient name and date
render where they did before, and the bottom-left rectangle
that previously rendered as blank now shows the same drawn
signature, composited as an inline image on top of the
original scan.

If either PDF comes back without the signature, the most
likely cause is the same one as the earlier tutorials — the
visitor's session was pinned to the pre-publish definition.
Close the tab, reopen the public URL, and submit again.

## Next

Continue with [Promote a submission to an entity](/docs/forms/tutorials/201-promote-to-entity).
