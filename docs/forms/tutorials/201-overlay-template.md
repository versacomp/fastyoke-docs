---
title: "Place an Overlay template"
summary: "Drop value regions onto any PDF — works on scans, no AcroForm needed."
order: 6
tier: "201"
---

# Place an Overlay template

::callout{type="info" title="What you'll build"}
The `Patient Intake` form now produces two PDFs on every
submission: the AcroForm `consent.pdf` from the previous tutorial
*and* an overlay-filled `hipaa-acknowledgment.pdf`. The second
file is a flat scan of a paper acknowledgment — no AcroForm
widgets to bind to, just ink — and the overlay renderer paints
the patient's name and the submission date on top of it.
::

::callout{type="note" title="Prerequisites"}
Finish [Attach an AcroForm template](/docs/forms/tutorials/201-acroform-template)
first. This tutorial assumes the `Patient Intake` form already
has a published definition with the `full_name` field on it.
::

The AcroForm path only works when the source PDF carries named
form widgets. A lot of real-world paperwork doesn't —
compliance hands you a scanned acknowledgment, the carrier sends
a flattened export, the front office prints and re-scans the
template every year. Overlay templates handle exactly that case.
You upload the flat PDF, mark rectangles on the page where the
values should go, and the renderer composites the typeset
values on top at submit time. The original page bytes are
untouched.

## 1. Add an Overlay template to the form

Open **Forms**, click into `Patient Intake`, and in the right
rail open the **PDF templates** panel. Below the AcroForm row
you added in the last tutorial, click **Add Overlay**.

The panel switches into upload mode.

## 2. Upload the source PDF

Drop `hipaa-acknowledgment.pdf` onto the upload zone. The file
is a 1-page US-Letter scan (612 × 792 pt) with no form widgets.

The builder runs an intake pass on the upload, records the page
geometry, and opens the page preview. If the intake fails —
encrypted PDF, corrupted xref, page count over the per-template
cap — the panel surfaces the failure class inline and refuses
the upload. Re-export from the source tool and try again.

## 3. Place the regions

The preview shows page 1 of the acknowledgment. Click-and-drag
on the page to draw a rectangle, then pick the region's **kind**
and **binding** in the inspector that pops out on the right.
Three region kinds matter for this template:

- A **text** region for the patient name, near the top-left.
- A **text** region for the submission date, near the top-right.
- A **text** region acting as a signature placeholder, near the
  bottom-left. We leave this one bound to a literal empty string
  for now — the next tutorial swaps it for a real signature
  region wired to a `signature` field.

Draw all three. Approximate placements (PDF points, bottom-left
origin):

- Patient name — page 1, x=72, y=620, w=300, h=18 — bound to
  the `full_name` form field.
- Date — page 1, x=420, y=620, w=120, h=18 — bound to the
  `__submitted_at` system token.
- Signature placeholder — page 1, x=72, y=120, w=200, h=60 —
  bound to a literal empty string.

The mapping you just authored serializes like this:

```json
{
  "regions": [
    {
      "id": "r_patient_name",
      "page": 1,
      "x": 72.0,
      "y": 620.0,
      "w": 300.0,
      "h": 18.0,
      "kind": "text",
      "binding": { "type": "form_input", "key": "full_name" },
      "font_size_pt": null
    },
    {
      "id": "r_submitted_at",
      "page": 1,
      "x": 420.0,
      "y": 620.0,
      "w": 120.0,
      "h": 18.0,
      "kind": "text",
      "binding": { "type": "token", "token": "__submitted_at" },
      "font_size_pt": null
    },
    {
      "id": "r_signature_placeholder",
      "page": 1,
      "x": 72.0,
      "y": 120.0,
      "w": 200.0,
      "h": 60.0,
      "kind": "text",
      "binding": { "type": "literal", "value": "" },
      "font_size_pt": null
    }
  ]
}
```

The wire shape is the same as what the validator checks on save:
each region carries a stable `id`, a page number, four geometry
floats in PDF points, a `kind`, and a tagged `binding`. The
binding tag is one of `form_input`, `token`, `literal`,
`signature_field`, or `form_input_image`; the matrix that pairs
kinds with bindings is enforced server-side. A `checkbox` region
must bind to a `form_input`, a `signature` region must bind to a
`signature_field`, an `image` region must bind to a
`form_input_image`, and a `text` region accepts anything.

Here's what the placements look like against the page:

```text
+---------------------------------------------+
|                                             |
|  Patient: [____________]   Date: [______]   |   <- y≈620
|                                             |
|                                             |
|     ( body copy of the acknowledgment )     |
|                                             |
|                                             |
|                                             |
|                                             |
|  Sign: [____________________]               |   <- y≈120
|                                             |
+---------------------------------------------+
  ^                                           ^
  x=0                                       x=612
  (origin bottom-left, 1-page US-Letter)
```

::callout{type="note" title="Coordinate origin"}
Overlay coordinates use the PDF / PostScript convention: the
origin is the **bottom-left** corner of the page, `y` grows
upward, and the unit is one PDF point (1/72 inch). The page
preview reflects this — drag a region near the top of the
page and the inspector's `y` value reads ~620 on a US-Letter
sheet, not ~170. Live values update as you drag, so coarse
placement by eye and then nudge to the value you want.
::

## 4. Save

Click **Save**. The validator runs the closed kind/binding
matrix, checks every region sits inside its page's geometry,
and checks each `form_input` key exists on the published form
definition. Errors come back inline against the offending
region — the rest of the mapping stays in the editor while you
fix.

Then **Publish** to cut a new form definition version, the
same way you did for the AcroForm template. Existing public
links keep working; the next visitor session loads the version
with both PDF templates attached.

## Verify it worked

Open the public URL of `Patient Intake` in an incognito window,
fill the form out, and submit.

The submit response now carries two download URLs — one per
attached template. Open the `hipaa-acknowledgment.pdf` URL.
The patient's name appears at top-left where you drew the
first region, the submission timestamp appears at top-right,
and the signature placeholder region is blank — the literal
empty string renders as nothing, which is what we want until
the next tutorial wires a real signature in. The original
scan underneath is unchanged; only the three rectangles you
authored are overpainted.

If a value lands a few points off where you wanted it, open
the form, nudge the region's `y` (or `x`) in the inspector,
save, and re-submit. The renderer is deterministic — the same
`payload_json` plus the same mapping always produces a
byte-identical PDF — so once you've got the placement right
it stays right.

## Next

Continue with [Wire up the signature region](/docs/forms/tutorials/201-signatures).
