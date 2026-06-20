---
title: Error envelope
summary: The standard AppError JSON envelope, what each commonly-returned status code means in FastYoke, and three worked examples.
order: 2
---

# Error envelope

Every FastYoke API error follows the same JSON shape. This page
documents the envelope and what each status code means in
FastYoke specifically.

## The AppError envelope

Every 4xx and 5xx response is a JSON object with at least an
`error` machine code and a `message` human description, and
optionally a `details` object carrying field-level context:

```json
{
  "error": "validation_failed",
  "message": "field 'body' is required",
  "details": { "field": "body" }
}
```

The `error` code is stable and machine-readable; the `message` is
human-readable and may be reworded between releases. Client code
should branch on `error`, not on `message`.

## Status code semantics

| Status | Meaning in FastYoke |
|--------|---------------------|
| `400` | Request validation failure — missing fields, malformed JSON, bad enum value. |
| `401` | Missing or invalid JWT. The auth header was absent, expired, or didn't verify. |
| `403` | Authenticated, but not authorized for this resource — your JWT is valid but doesn't carry the permission this endpoint requires. |
| `404` | Not found in the caller's tenant. Could mean the resource genuinely doesn't exist, OR it exists in another tenant that this JWT doesn't see. The platform deliberately collapses both into 404 to avoid leaking cross-tenant existence. |
| `409` | Write conflict — concurrent FSM transition on the same job, double-seal attempt on an already-sealed envelope, etc. Safe to re-fetch the resource and decide whether to retry. |
| `422` | Semantically invalid — the request parsed and the caller had permission, but a business rule rejected it. FSM guard returned false; field constraint violated; envelope can't transition from its current state. |
| `429` | Rate limit hit. See [Rate limits](/docs/developers/rate-limits) for the per-endpoint limits and the `Retry-After` header. |
| `5xx` | Platform error. Safe to retry with exponential backoff. |

## Worked examples

### 404 on a wrong-tenant id

```sh
curl -i https://app.fastyoke.io/api/v1/tenant/jobs/job_abc123 \
  -H "Authorization: Bearer <jwt for tenant A>"
```

If `job_abc123` belongs to tenant B:

```http
HTTP/1.1 404 Not Found
Content-Type: application/json

{
  "error": "not_found",
  "message": "job not found"
}
```

The platform does NOT distinguish "doesn't exist" from "exists in
another tenant." This is by design — see
[Tenant scoping](/docs/developers/tenant-scoping).

### 422 on a guard-rejected FSM transition

```sh
curl -i -X POST https://app.fastyoke.io/api/v1/tenant/jobs/job_xyz/transition \
  -H "Authorization: Bearer <jwt>" \
  -d '{"event_type":"ship"}'
```

```http
HTTP/1.1 422 Unprocessable Entity
Content-Type: application/json

{
  "error": "guard_rejected",
  "message": "transition guard returned false",
  "details": { "from": "packed", "to": "shipped", "guard": "all_items_scanned" }
}
```

A 422 means the request was well-formed and authorized but the
FSM's guard logic refused the transition.

### 429 with Retry-After

```sh
curl -i -X POST https://app.fastyoke.io/api/v1/public/forms/anonymous \
  -d '{...}'
```

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 15
Content-Type: application/json

{
  "error": "rate_limited",
  "message": "too many requests"
}
```

Honor the `Retry-After` header. See
[Rate limits](/docs/developers/rate-limits) for the backoff
recommendation.
