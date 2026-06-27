---
title: "Attach an AcroForm template"
summary: "Upload a fillable PDF, map form fields to AcroForm fields, render on submit."
order: 5
tier: "201"
---

# Attach an AcroForm template

::callout{type="info" title="What you'll build"}
The `Patient Intake` form now generates a filled `consent.pdf` on
every submission. The submit response carries a `pdf_download_url`
the visitor can tap to grab their signed copy.
::

::callout{type="note" title="Prerequisites"}
Finish [Share with an invite or embed](/docs/forms/tutorials/101-share-invite-and-embed)
first. You need the published `Patient Intake` form and a public
URL you can submit against.
::

The share tutorials get visitors *to* the form. This one decides
what they take away when they're done. AcroForm is the path to use
when you already have a fillable PDF — a consent waiver from your
compliance team, an insurance form from a carrier, anything with
named fields already drawn on the page. FastYoke reads the
widgets out of the PDF, you map each one to a form field, and the
renderer fills the values and flattens the result on submit.

## 1. Add an AcroForm template to the form

From the admin shell, open **Forms** and click into `Patient
Intake`. In the right rail open the **PDF templates** panel and
click **Add AcroForm**.

The panel switches into upload mode and asks for the source PDF.

## 2. Upload the fillable PDF

Drop `consent.pdf` onto the upload zone. The builder parses the
file server-side and lists every AcroForm widget it found:

- `Patient Name` — text widget
- `Date of Birth` — text widget
- `Email` — text widget
- `Today` — text widget

If the inventory comes back empty or names look garbled, the source
file probably isn't a true AcroForm — it's a scanned image or a
flattened PDF. Re-export it from the original tool with form fields
enabled and try again.

### What happens on upload

The upload runs through a server-side step that **cleans the file,
then validates it**. Two kinds of content are removed automatically —
you no longer have to fix them by hand:

- **Embedded JavaScript is stripped.** Acrobat silently adds format,
  calculate, and validate scripts to fields (a date or currency field
  is the usual source). FastYoke removes them on upload, so the stored
  template and every rendered copy are script-free.
- **Active actions are stripped** — links, launch, submit-form, and
  remote-go-to actions are removed for the same reason.

::callout{type="tip" title="Fonts are no longer a concern"}
FastYoke renders your field values with a standard built-in font, so
the source PDF's fonts don't matter. An Arial form, a non-embedded
font, anything — it uploads and renders fine. (There's nothing to fix
and no font requirement to meet.)
::

After cleaning, a file is rejected only for a genuine **structural**
problem the cleaner can't safely fix. A failed upload shows `PDF
validation failed` followed by one or more of these codes:

- `encrypted_pdf_unsupported` / `malformed_pdf` — the file is
  password-protected or can't be parsed. Re-export an unencrypted,
  well-formed PDF.
- `xfa_form_unsupported` — an XFA (LiveCycle) form. Re-save as a
  standard AcroForm.
- `crypto_signature_widget` — a digital-signature field. Use
  FastYoke's own signature token instead of a baked-in signature
  widget.
- `duplicate_field_name` — two fields share a name. Rename one so
  every field is unique.
- `unsupported_widget_type` — a widget that isn't text, button,
  choice, or signature. Rebuild it as one of those.
- `too_many_fields` / `template_too_large` — the form exceeds the
  field-count or file-size limit. Split it or trim unused fields.

## 3. Map AcroForm fields to form values

Each widget in the inventory needs a binding. The builder shows a
two-column editor: the AcroForm field name on the left, the source
on the right. There are three source kinds:

- **Form field** — bind to a payload key from the `Patient Intake`
  form. The most common choice.
- **System token** — bind to one of `__submitted_at`,
  `__submitter_signature`, `__submitter_ip`, `__form_version`.
- **Literal** — a fixed string up to 200 characters.

Pick **Form field** for `Patient Name`, `Date of Birth`, and
`Email`, and choose the matching payload key from each dropdown.
Pick **System token** → `__submitted_at` for `Today`.

The stored mapping looks like this on the wire:

```json
{
  "entries": {
    "Patient Name": { "type": "form_field", "key": "full_name", "input_type": "text", "format_as_string": false },
    "Date of Birth": { "type": "form_field", "key": "dob", "input_type": "date", "format_as_string": false },
    "Email": { "type": "form_field", "key": "email", "input_type": "email", "format_as_string": false },
    "Today": { "type": "system_token", "token": "__submitted_at" }
  }
}
```

The validator runs on save and rejects any binding whose input
type isn't compatible with the widget's type — a `checkbox` widget
won't accept a `text` field, for example. The editor surfaces those
errors inline against the offending row, so you fix and re-save
without losing the rest of the mapping.

::callout{type="tip" title="Deterministic rendering"}
The same `payload_json` always produces a byte-identical PDF. The
renderer is contract-tested to enforce this.
::

If the visitor needs to combine two fields into one widget — first
name plus last name into `Patient Name`, say — add a single
`full_name` field to the form itself and bind that. AcroForm
bindings are one widget to one source by design; the form schema
is where you compose.

## 4. Turn on render-on-submit

Below the mapping editor is the **Render on submit** toggle. Off
by default — the template uploads and validates, but no rendering
happens until you opt in. Flip it on.

With the toggle on, every submission triggers a render attempt
right after the row is written. The submit response gains a
`pdf_download_url` field, an HMAC-signed URL valid for 15 minutes.
Render failures don't block ingestion — the submission still
succeeds, the field is just omitted from the response.

## 5. Save and re-publish

Click **Save** at the top of the form. Then **Publish** to cut a
new definition version.

The re-publish step is the one detail to get right. Existing
invite tokens and the embed snippet keep pointing at the form's
public token across versions, but the template binding rides on
the published definition. Without a republish your kiosk URL still
loads the form, accepts the submission, and just doesn't render
the PDF — because the visitor's session is pinned to the previous
version. Republish, and the next visitor hits the version with the
template attached.

## Verify it worked

Open the public URL of `Patient Intake` in an incognito window —
the same one you used at the end of the share tutorial. Fill in
the form and submit.

In the browser devtools open the network tab and find the
`POST /api/v1/public/forms/<token>/submit` request. The response
JSON includes:

```json
{
  "submission_id": "01HXY…",
  "processing_state": "received",
  "submitted_at": "2026-06-06T14:22:11Z",
  "pdf_download_url": "https://tenant.fly.dev/api/v1/public/attachments/…?sig=…&exp=…"
}
```

Open `pdf_download_url` in a new tab. The browser loads
`consent.pdf` with the four widgets filled: the patient's name in
`Patient Name`, the date in `Date of Birth`, the email in `Email`,
and the submission timestamp in `Today`. If a `--template nextjs`
consumer app is wrapping the form, the URL renders as a
"Download your signed copy" link on the success screen
automatically.

If `pdf_download_url` is missing from the response, the most
likely cause is that the visitor's session was loaded against the
pre-publish definition. Close the tab, re-open the public URL,
and submit again — the new session picks up the version with the
template attached.

If the field is present but the URL 404s after the click, the
render failed silently. Open the `Patient Intake` form in the
admin shell, switch to **Submissions**, and click the offending
row. The PDF tab shows the renderer's failure class —
`mapping_invalid`, `widget_missing`, `pdf_corrupt`, and so on —
along with the audit row written at attempt time.

## Next

Continue with [Attach an Overlay template](/docs/forms/tutorials/201-overlay-template).
