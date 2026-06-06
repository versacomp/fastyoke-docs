---
title: "File uploads and scanning"
summary: "Collect insurance card images; watch them flow through the three-layer scanner."
order: 11
tier: "301"
---

# File uploads and scanning

::callout{type="info" title="What you'll build"}
`Patient Intake` learns to collect insurance card images. You'll
add `insurance_card_front` and `insurance_card_back` file fields
to the `insurance` page, submit a happy-path image, then submit
a deliberately bad file so you can watch the three-layer scanner
transition each upload from `pending` to `clean` — or `rejected`.
::

::callout{type="note" title="Prerequisites"}
Finish [Conditional routing](/docs/forms/tutorials/301-conditional-routing).
You need an `insurance` page and a published `Patient Intake`
form with the age-based branch wired up.
::

The public renderer doesn't bundle file bytes into the submit
POST. Each file uploads on its own request the moment a user
picks it, lands in `form_submission_attachments` with
`scan_status='pending'`, and only gets claimed by the
submission INSERT once the three-layer scanner clears it. That
split is why the upload widget has a state to display in the
first place — by the time the renderer is ready to POST, the
attachment row already exists and is moving through scanner
states server-side.

## 1. Add the file fields

In the Forms builder, open `Patient Intake` and switch to the
`insurance` page. From the field palette, drag in a **File
upload** field for `insurance_card_front`. Configure it:

- `accepted_mime`: `image/jpeg, image/png`
- `max_size`: `5` (MiB)
- `multiple`: `false`

Drag in a second file field for `insurance_card_back` with the
same three settings. We keep `multiple` off because each card
has exactly one front and one back — two single-file fields
read more cleanly than one multi-file field, and they let the
renderer label the slots in the UI without extra prose.

The `accepted_mime` list is enforced two ways: the renderer's
`<input accept>` filters the OS file picker, and the MIME-magic
scanner re-checks the bytes after upload. The browser-side
filter is convenience; the magic check is the gate.

## 2. Save and re-publish

Click **Save**, then **Publish**. The published version row
freezes the file-field config the same way it froze the routing
graph in the previous tutorial. The public renderer at the
invite URL picks up the new fields on the next request.

```text
┌─────────┐      ┌──────────┐      ┌───────┐
│ pending │ ───▶ │ scanning │ ───▶ │ clean │
└─────────┘      └────┬─────┘      └───┬───┘
                     │                │
                     ▼                ▼
               ┌──────────┐    submit handler claims
               │ rejected │    attachments atomically
               └──────────┘    with submission INSERT
```

Every uploaded attachment walks this graph. `pending` is the
row's birth state — the bytes have landed on disk but no
scanner has touched them yet. `scanning` covers the three
layers in sequence: MIME magic on the leading bytes,
VirusTotal hash lookup, and an LLM content classifier for
image and document payloads. `clean` is the only terminal
state the renderer will POST against; `rejected` carries a
human-readable `scan_reason` the widget surfaces in place of
the green check.

## 3. Submit a happy-path image

Open the public invite URL. Walk through `demographics` and
`contact` with an adult age (so routing keeps you on the
`insurance` page). Pick a normal JPEG for
`insurance_card_front` — a phone photo of any rectangular
card works.

Watch the widget. The label transitions through `pending` →
`scanning` → `clean`, usually within a second or two for a
small image. Repeat for `insurance_card_back`. Once both
slots are green, the **Submit** button enables and the
submission POSTs.

::callout{type="warn" title="Pending uploads block submit"}
The renderer refuses to POST the submission while any
attachment is still `scanning`. The backend's claim predicate
(<code>scan_status='clean' AND submission_id IS NULL</code>)
would 422 it anyway — failing client-side gives the user a
clearer error and avoids a wasted round-trip.
::

## 4. Submit a deliberately bad file

Reload the public form and reach the `insurance` page again.
This time, take any tiny `.exe` (Windows binaries, even
trivial ones, work — what matters is the leading bytes are not
a JPEG or PNG header) and rename it to `card.png` so the
browser MIME filter lets it through the file picker.

Upload it as `insurance_card_front`. The widget shows
`pending`, flips to `scanning`, then lands on `rejected`. The
rejection reason renders inline — something like *"MIME magic
mismatch: declared image/png, detected application/x-dosexec."*
The slot stays red and the **Submit** button stays disabled
until you replace the file with a clean image.

The other two scanner layers behave the same way from the
renderer's perspective: a VirusTotal hash hit produces a
*"known malicious file"* reason; an LLM content rejection
produces a category-specific reason ("explicit content
detected", etc.). Same `rejected` state, different
`scan_reason`.

## Verify it worked

Submit the form once with two clean card images.

- The bad-upload attempt was rejected with a human-readable
  reason in the widget; nothing reached the database beyond
  the original `form_submission_attachments` row marked
  `rejected`.
- The clean upload reached `clean`, the submission POSTed,
  and both attachment rows were claimed atomically with the
  submission INSERT.
- After promoting the submission to a `patient` entity, the
  two attachments appear in `entity_files` tied to the
  promoted record — the promotion path re-parents the
  attachments by id, no re-upload required.

If all three line up, the file-upload pipeline is live.

## Next

Continue with [Anti-abuse](/docs/forms/tutorials/301-anti-abuse).
