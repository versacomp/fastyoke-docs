# HTTP ingestion API

> Server-to-server submission of form data via REST. Authenticate with a tenant API token, post a JSON payload, get a submission ID back.

# HTTP ingestion API

The HTTP ingestion API lets your backend submit form data programmatically
without a browser or form widget. One `POST` per submission — you get a
submission ID back, and the result is indistinguishable from a user filling
the form manually.

> **Pro+ required**
>
> The ingestion endpoint is available on Pro, Team, Enterprise, and Fleet
>   tiers. Hobby tenants receive a `422` with error code `tier_required`.
>   See [Forms tier entitlements](/docs/forms/tiers) for the full matrix.

---

## Endpoint

```
POST /api/v1/tenant/forms/:form_id/submissions
```

`:form_id` is the UUID of the form definition. The form must be in
`published` status; submitting to a draft or archived form returns `422
form_not_accepting_submissions`.

---

## Authentication

Pass a tenant API token as a Bearer token in the `Authorization` header.

```http
Authorization: Bearer fy_pat_<your-token>
```

The token must carry the `forms:ingest` scope. Tokens with `admin:*` are
also accepted. To mint a token with the correct scope, go to **Settings →
API tokens** in the admin portal.

If the token is missing or does not have the required scope, the endpoint
returns `401 Unauthorized` or `403 Forbidden` respectively.

---

## Request

### Headers

| Header | Required | Description |
| --- | --- | --- |
| `Authorization` | Yes | `Bearer fy_pat_<token>` |
| `Content-Type` | Yes | `application/json` |
| `Idempotency-Key` | No | Deduplication key — see [Idempotency](#idempotency) |
| `X-Client-Now` | No | RFC 3339 timestamp from the submitting device; stored in submission metadata. Ignored if malformed. |

### Body

```json
{
  "payload": {
    "field_key": "value"
  }
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `payload` | object | Yes | Key-value map of field answers. Keys must match the field keys defined in the form schema. |
| `client_ip` | string | No | Override the IP address recorded in submission metadata. Only honoured when the token also holds the `forms:ingest:trust_client_ip` scope (for proxied / gateway senders). Ignored otherwise. |

The field values in `payload` are validated against the form's published
schema. Required fields must be present; type constraints (text, number,
email, etc.) are enforced. Validation failures return `422
validation_failed` with a per-field error list.

---

## Response

### 201 Created — new submission

```json
{
  "submission_id": "01jx3p...",
  "was_idempotent_replay": false
}
```

### 200 OK — idempotent replay

When an `Idempotency-Key` header was supplied and a matching prior
submission exists, the endpoint returns `200` instead of `201` with the
original `submission_id` and `"was_idempotent_replay": true`. No duplicate
row is written.

```json
{
  "submission_id": "01jx3p...",
  "was_idempotent_replay": true
}
```

---

## Idempotency

Include an `Idempotency-Key` header to make requests safe to retry.

```http
Idempotency-Key: order-12345-attempt-1
```

**Key format:** 1–128 characters, allowed characters `A-Z a-z 0-9 . _ -`.
Any other characters return `400 bad_idempotency_key_format`.

**How it works:**

1. On the first request with a given key, the submission is written and
   the key is associated with the resulting `submission_id`.
2. Retrying with the same key **and the same payload** returns `200` with
   the original `submission_id`. No second submission is created.
3. Retrying with the same key but a **different payload** returns `409
   idempotency_key_payload_mismatch`. The key cannot be reused with
   different data.

**TTL:** Idempotency records expire after 24 hours. After expiry, the same
key may be reused and will be treated as a fresh submission.

**Payload hashing:** Sameness is determined by a canonical SHA-256 hash of
the `payload` object. Object key ordering is normalized before hashing, so
`{"b": 2, "a": 1}` and `{"a": 1, "b": 2}` are treated as identical.

**Concurrency:** If two requests arrive simultaneously with the same key,
one wins and proceeds; the other spins briefly and then returns `200` with
the winner's `submission_id`. If the key is still in flight after the retry
budget is exhausted, a `422 in_flight` response is returned — wait a moment
and retry.

---

## Rate limiting

The endpoint is rate-limited to a burst of **60 requests** per
`(token, form)` pair. After exhausting the burst, requests return
`429 Too Many Requests` with a `Retry-After` header and a
`retry_after_secs` field in the response body.

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 3

{
  "error": {
    "code": "rate_limited",
    "retry_after_secs": 3,
    "message": "rate limited"
  }
}
```

---

## Error reference

All errors share the envelope shape `{ "error": { "code": "...", "message": "..." } }`.
Some codes include additional fields noted below.

| HTTP status | Error code | Description |
| --- | --- | --- |
| `400` | `bad_idempotency_key_format` | `Idempotency-Key` contains disallowed characters or exceeds 128 chars. |
| `400` | `malformed_json` | Request body is not valid JSON. |
| `401` | `unauthorized` | `Authorization` header is absent or the token is revoked. |
| `403` | `scope_required` | Token exists but does not have `forms:ingest` scope. Response includes `"required": "forms:ingest"`. |
| `404` | `form_not_found` | No published form with that ID belongs to your tenant. Also returned for cross-tenant ID guessing. |
| `409` | `idempotency_key_payload_mismatch` | The idempotency key was previously used with a different payload. |
| `422` | `tier_required` | Your tenant is on Hobby tier. Response includes `"required": "pro"` and `"current": "hobby"`. |
| `422` | `form_not_accepting_submissions` | The form is not in `published` status. |
| `422` | `validation_failed` | One or more fields failed schema validation. Response includes `"field_errors": [{ "field": "...", "message": "..." }]`. |
| `422` | `in_flight` | An idempotency key is being processed by a concurrent request. Retry after a short delay. |
| `429` | `rate_limited` | Burst quota exceeded. Respect the `Retry-After` header. Response includes `"retry_after_secs"`. |
| `500` | `internal` | Unexpected server error. The body message is always `"internal server error"` — no internal details are leaked. |

---

## Example

```bash
curl -X POST https://app.fastyoke.io/api/v1/tenant/forms/FORM_ID/submissions \
  -H "Authorization: Bearer fy_pat_YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: my-batch-job-row-42" \
  -d '{
    "payload": {
      "name": "Alice",
      "email": "alice@example.com"
    }
  }'
```

Successful response:

```json
{
  "submission_id": "01jx3pabcdefghijklmn",
  "was_idempotent_replay": false
}
```
