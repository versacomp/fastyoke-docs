---
title: Test a form locally with the HTTP ingestion API
summary: Skip the UI — POST a payload directly to verify your wiring.
order: 10
recipe: true
---

# Test a form locally with the HTTP ingestion API

When you'd use this — you're debugging a Submit action or a downstream
promotion and you don't want to drive the browser every iteration. One
curl exercises the same validation, idempotency, and submission write
path the public widget uses.

## 1. Mint a PAT

In the admin shell, open **Settings → API tokens** and click **New**.
Give it the `forms:ingest` scope, copy the `fy_pat_…` value
immediately (it's only shown once), and stash it in a shell variable
or your secrets manager. See the
[API tokens reference](/docs/auth) for scope details.

## 2. POST a submission

Substitute the published form's UUID for `FORM_ID`:

```bash
curl -X POST http://localhost:8080/api/v1/tenant/forms/FORM_ID/submissions \
  -H "Authorization: Bearer fy_pat_…" \
  -H "Content-Type: application/json" \
  -d '{"payload": {"first_name": "Test"}}'
```

Field keys inside `payload` must match the published schema. Required
fields not in the body come back as `422 validation_failed` with a
per-field error list — handy for catching a typo in a field key
without round-tripping through the UI.

## 3. Inspect the response

A fresh submission returns `201 Created`:

```json
{ "submission_id": "01jx3p…", "was_idempotent_replay": false }
```

Open the admin **Submissions** list and the row appears with the same
`submission_id`. If you're testing a promote-on-submit hookup, the
paired entity should already exist by the time the row renders. Any
downstream Action runs identically to a browser submission.

## See also

- [Server-to-server submission tutorial](/docs/forms/tutorials/301-http-ingestion)
- [HTTP ingestion reference](/docs/forms/http-ingestion)
