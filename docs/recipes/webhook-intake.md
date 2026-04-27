# Webhook Intake

> Receive external events as FSM-backed jobs via a public form + invite token.

# Webhook Intake

FastYoke doesn't have a dedicated "webhook endpoint" primitive — it
doesn't need one. Public forms are the intake surface. Any external
system that can POST JSON can submit to
`/api/v1/public/forms/:token/submit`, and the `SPAWN_JOB_FROM_FORM`
action turns the submission into an FSM-backed job with full audit
trail, scanner, and quarantine semantics.

This recipe walks a CRM-fires-a-webhook scenario. Substitute any
external system that can POST JSON.

## 1. Define the shape you'll receive

Decide up front what your external system will send. This becomes
your form's field schema. Example:

```json
{
  "customer_email": "alex@example.com",
  "plan": "enterprise",
  "notes": "Requested by account manager."
}
```

## 2. Build the intake form

In the admin shell at `/admin/forms`:

1. Create a form named **CRM Intake**, slug `crm-intake`.
2. Add three fields matching the JSON shape:
   - `customer_email` — type `email`, required.
   - `plan` — type `select`, options `starter / pro / enterprise`.
   - `notes` — type `textarea`, optional, max_length 2000.
3. **Publish** the form.

> **Mirror an existing entity**
>
> If your CRM payload should eventually become an entity record,
>   use the **From entity** tab to import the fields straight from
>   your annotated entity definition — labels, `required`, and
>   enum options carry over automatically (see
>   [Entities](/docs/entities)).

## 3. Wire SPAWN_JOB_FROM_FORM to your workflow

Attach the form to the FSM schema that should consume each
submission. On the form's detail page, in the **Submit actions**
panel, pick your FSM schema from the <em>Spawn job in schema</em>
dropdown. Save.

Every accepted submission now spawns a fresh job in the schema's
initial state and links the submission (`processing_state =
'attached'`) to it. The submit API's response carries both the
`submission_id` and the new job is visible under the form's
Submissions tab.

## 4. Mint an invite token

Public form endpoints are **token-gated**. No anonymous POSTs — a
stolen URL has a bounded blast radius (per-token expiry + optional
submission cap). From the form's detail page:

- **Expiry**: pick a horizon long enough for your CRM to run. One
  year is fine for a machine-to-machine token.
- **Submission cap**: leave blank for unbounded, or set a number
  if you want hard backpressure.

Copy the resulting URL; it looks like `/f/<token>`. The HTTP
endpoint your CRM will POST to is
`/api/v1/public/forms/<token>/submit`.

## 5. Have your CRM POST

```bash title="curl"
curl -X POST "https://fastyoke.example/api/v1/public/forms/<token>/submit" \
  -H "content-type: application/json" \
  -d '{
    "payload": {
      "customer_email": "alex@example.com",
      "plan": "enterprise",
      "notes": "Requested by account manager."
    }
  }'
```

```bash title="node (fetch)"
await fetch(`${BASE}/api/v1/public/forms/${TOKEN}/submit`, {
  method: 'POST',
  headers: { 'content-type': 'application/json' },
  body: JSON.stringify({
    payload: {
      customer_email: 'alex@example.com',
      plan: 'enterprise',
      notes: 'Requested by account manager.',
    },
  }),
});
```

The response is the canonical `submission` shape — `{ id,
submitted_at, submission_count }` — so your CRM can record the
correlation id.

## Error responses you should handle

| HTTP | Meaning | What to do |
|---|---|---|
| `404` | Unknown token (typo or revoked) | Regenerate, update secret |
| `410` | Expired OR submission cap reached | Mint a fresh token |
| `422` | Payload failed schema validation | Inspect `error` body, fix shape |
| `429` | Rate limited (per-token) | Back off + retry |

## Security posture

- **Quarantine**: submissions land in `form_submissions`, not in
  `entity_records`. Promotion to an entity happens via an explicit
  `PROMOTE_FORM_TO_ENTITY` FSM action — a malformed webhook can't
  poison your real data.
- **Scan**: any file attachment goes through the three-layer scanner
  (MIME magic → VirusTotal → LLM). See [Forms Builder](/docs/forms/builder#file-attachments)
  for details.
- **Audit**: every submission + transition writes an immutable
  `event_log` row. You can prove what the CRM sent + when it was
  acted on.

> **Don**
>
> It's tempting to reuse a tenant admin JWT for CRM-to-FastYoke
>   calls. Don't — user JWTs are 24h-scoped and revoke on logout.
>   A form invite token is a purpose-built, revocable, per-form
>   credential. Use the right primitive.

## Related

- [Forms Builder](/docs/forms/builder) — field types, routing, themes.
- [Authentication](/docs/auth) — JWT + access-token shapes if you
  need something broader than an invite token.
