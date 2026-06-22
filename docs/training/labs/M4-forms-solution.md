---
title: Lab M4 — Solution
summary: Reference solution for Lab M4 — sample form schema, exact public-submit curl with multipart body, FSM job inspection, and the 10-iteration anti-abuse loop with expected 429 + Retry-After.
order: 9
---

# Lab M4 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M4](/docs/training/labs/M4-forms). Work the lab
first — the friction of clicking through is intentional.
::

## Answer per step

### Step 1 — Form schema shape

In the admin shell at **Settings → Forms → New form**,
the field definitions for a sample patient-intake form:

```json
{
  "title": "Patient intake",
  "fields": [
    {"name": "name",    "type": "text",     "required": true},
    {"name": "urgency", "type": "select",   "options": ["low","medium","high"]},
    {"name": "scan",    "type": "file",     "accept": ["image/png","image/jpeg"]},
    {"name": "consent", "type": "checkbox", "required": true}
  ]
}
```

Forms validate submissions against a **zod-mirrored**
schema — the same shape the admin-shell frontend uses
client-side becomes the server-side validator on
submit. One source of truth across both sides.

### Step 2 — Publish + the public URL

After saving the form draft, the **Publish** action
returns a tokenized URL of the shape
`https://www.fastyoke.io/myform/<token>`. Opening it in
incognito loads the form without an auth prompt.

### Step 3 — Submit anonymously via curl

```bash
curl -i -X POST \
  -F 'name=Jane Doe' \
  -F 'urgency=medium' \
  -F 'scan=@./sample.png' \
  -F 'consent=true' \
  https://www.fastyoke.io/api/v1/public/forms/<token>/submit
```

Expected: `HTTP/2 200` with a JSON body containing
`submission_id`. The admin shell's submission view shows
the new row immediately.

### Step 4 — Inspect the FSM job

```bash
curl -i -H "Authorization: Bearer $FY_TEST_TOKEN" \
  "https://www.fastyoke.io/api/v1/tenant/jobs?limit=5"
```

Expected: `HTTP/2 200`. The most recent job entry in the
response has a `source` field (or equivalent) that names
your form submission's id.

The form-to-FSM wiring lives in the form definition;
forms can either land a submission as data only or
trigger a workflow at submit. The trigger fires
synchronously inside the submit handler, so the job
exists by the time the 200 returns.

### Step 5 — Anti-abuse trigger

```bash
for i in {1..10}; do
  curl -fsS -i -X POST \
    -F 'name=spam' -F 'urgency=high' -F 'consent=true' \
    https://www.fastyoke.io/api/v1/public/forms/<token>/submit \
    | head -n 3
  echo
done
```

After a few iterations the platform's rate limiter trips:

```
HTTP/2 429
Retry-After: 30
content-type: application/json

{"error":"rate_limited"}
```

The `Retry-After` value is in seconds. The exact
threshold may vary by tenant; this is intentionally not
pinned by invariant — what's pinned is the
`429 + Retry-After` contract.

## A note on the public-link auth model

Public-link forms are authenticated by the **token in
the URL**, not by a session or a PAT. The token is
tenant-scoped, form-scoped, and revocable from the
admin shell. The platform never trusts a `tenant_id`
the submitter supplies; it derives the tenant from the
token's lookup row.

## Back to the lab

[Lab M4 — Public-link form](/docs/training/labs/M4-forms).
