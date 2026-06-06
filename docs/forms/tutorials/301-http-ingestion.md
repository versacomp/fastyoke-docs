---
title: "Server-to-server submission"
summary: "Pipe an upstream scheduler's POSTs into the form via the HTTP ingestion API."
order: 14
tier: "301"
---

# Server-to-server submission

::callout{type="info" title="What you'll build"}
An upstream scheduling system posts JSON straight at `Patient Intake`
and the row lands in the submissions queue without anyone touching
the public renderer. The result is indistinguishable from a walk-in
patient filling the form on a kiosk.
::

::callout{type="note" title="Prerequisites"}
Finish [Custom CSS](/docs/forms/tutorials/301-custom-css). You need a
published `Patient Intake` form and its form ID — copy it from the
URL of the builder or from the form's row in **Forms → All forms**.
::

The HTTP ingestion API is one `POST` per submission. The form's
published schema is the contract: required fields must be present,
typed fields are validated, and the response hands you a
`submission_id` you can store upstream.

## 1. Mint a PAT

Open the admin portal and go to **Settings → API tokens → New**. Give
the token a name a human will recognize three months from now
(`scheduler-prod-ingest` beats `token-1`), then pick the scope
`forms:ingest`. The portal shows the raw `fy_pat_…` value exactly
once — copy it into your scheduler's secret store before you close
the dialog. There is no recovery flow; a lost token is revoked and
reminted.

PAT semantics, scopes, and revocation are covered in
[Authentication & Scopes](/docs/auth). The short version: PATs are
SHA-256 hashed at rest, scoped explicitly, and never expire until
you revoke them.

## 2. POST the submission

The endpoint is:

```
POST /api/v1/tenant/forms/:form_id/submissions
```

`:form_id` is the UUID of the published `Patient Intake` form.
Submit with the PAT as a Bearer token and a JSON body whose
`payload` object maps onto the form's field keys.

```bash
curl -X POST https://app.fastyoke.io/api/v1/tenant/forms/FORM_ID/submissions \
  -H "Authorization: Bearer fy_pat_YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "payload": {
      "first_name": "Alice",
      "last_name": "Nguyen",
      "dob": "1986-03-14",
      "email": "alice.nguyen@example.com",
      "phone": "+1-555-0142"
    }
  }'
```

The keys inside `payload` must match the field keys you set when you
built `Patient Intake` — not the labels. If the scheduler emits
slightly different names (`patient_first_name`, `birthdate`), remap
them upstream rather than renaming fields in the form; the field
keys are part of the schema and changing them invalidates earlier
submissions in queries.

If you plan to retry on network errors, add an `Idempotency-Key`
header keyed off the upstream record (`scheduler-row-91824`). A
retry with the same key and the same body returns `200` with the
original `submission_id` instead of writing a duplicate row.

## 3. Read the response

On success the endpoint returns `201 Created`:

```json
{
  "submission_id": "01jx3pabcdefghijklmn",
  "was_idempotent_replay": false
}
```

Store the `submission_id` against the upstream record so the
scheduler can prove the handoff happened. If you sent an
`Idempotency-Key` and this is a replay, the status is `200 OK` and
`was_idempotent_replay` is `true` — the original `submission_id` is
returned and no new row is written.

The error envelope is `{ "error": { "code": "...", "message": "..." } }`.
A `422 validation_failed` includes a `field_errors` array naming the
field key and the reason — that's where you'll catch missing
required fields or a malformed `dob`. See
[HTTP ingestion API](/docs/forms/http-ingestion) for the full code
table.

## 4. Confirm in the admin

Open **Forms → Patient Intake → Submissions**. The row appears at
the top of the list with the same timestamp the server stamped, the
field values you posted, and the source labelled as an ingest. The
submission is a first-class row: it shows up in exports, in CSV
downloads, and in any downstream FSM job the form fires.

::callout{type="info" title="Ingest vs. public submit"}
The ingest endpoint requires a `fy_pat_` token with the
`forms:ingest` scope — it is admin-authenticated, not anonymous. The
public-submit flow at `POST /forms/:slug/submit` is the inverse: no
auth, but Turnstile / honeypot / daily cap shields apply. The
ingest path skips Turnstile because the token is the proof of
identity; rate limiting still applies (a burst of 60 requests per
`(token, form)` pair, then `429` with `Retry-After`). Don't reach
for ingest to bypass abuse controls — reach for it when the caller
is a trusted backend.
::

## Verify it worked

- **Row landed.** Open **Patient Intake → Submissions** and confirm
  the new row's field values match the JSON you posted. Click into
  the row and confirm `first_name`, `last_name`, `dob`, `email`, and
  `phone` are all populated.
- **Source is ingest.** In the submission detail panel, the source /
  origin metadata identifies the row as an API ingest rather than a
  public-renderer submit. If your tenant surfaces a metadata badge,
  it reads "ingest"; if it surfaces the raw user-agent, you'll see
  your scheduler's HTTP client string instead of a browser UA.
- **Idempotency holds.** Re-run the same `curl` with the same
  `Idempotency-Key` and confirm the response status flips to `200`
  with `"was_idempotent_replay": true` and the same `submission_id`.
  The submissions list does not grow a second row.

If all three checks pass, the upstream scheduler can hand off to
`Patient Intake` without any human touching the form.

## Next

Continue with [Vanity filenames](/docs/forms/tutorials/301-vanity-filenames).
