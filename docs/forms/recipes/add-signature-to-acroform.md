---
title: Add a signature line to an AcroForm template
summary: Bind a signature field to a PDF widget — same captured image flows in.
order: 9
recipe: true
---

# Add a signature line to an AcroForm template

When you'd use this — your AcroForm PDF has a `Signature` widget you've
never wired up. The visitor sees no signature pad on the form yet, and
the rendered PDF comes back with an empty signature box. One form-side
field plus one mapping entry connects them.

## 1. Add the signature field

Open the form in the admin shell and drag a **signature** field onto
the page where the consent lives. Set the **key** to `signature` and
mark it **required**. The published schema now carries
`{ "key": "signature", "type": "signature", "required": true }`, which
is the payload key the AcroForm renderer will reach for at render
time.

## 2. Bind the widget in the field mapper

Open the form's **PDF templates** panel and click into the AcroForm
template row. The widget inventory lists the `Signature` widget — a
real PDF signature field, not a text widget, so the mapper's validator
only accepts a signature-shaped binding.

Pick **System token** in the dropdown for the `Signature` row and
choose `__submitter_signature`. The saved entry looks like this:

```json
{ "Signature": { "type": "system_token", "token": "__submitter_signature" } }
```

The validator pairs the token with the widget's declared type on save
— if the widget weren't a signature widget the row would come back
with a `type_mismatch` error.

## 3. Save and re-publish

Save the template and click **Publish** on the form. The next
submission's `pdf_download_url` returns a `consent.pdf` with the
visitor's drawn signature composited into the widget as a native PDF
signature field, not a flattened bitmap.

## See also

- [Collect signatures tutorial](/docs/forms/tutorials/201-signatures)
