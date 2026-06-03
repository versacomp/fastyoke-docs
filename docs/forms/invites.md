---
title: Form invites (Mint)
summary: Issue named, revocable invite links to public forms. The Mint endpoint produces a URL anyone can submit; revoked invites return 410.
order: 35
---

# Form invites (Mint)

An invite token is a shareable URL that grants anonymous access to a single
published form. Any holder of the URL can load and submit the form — no login
required. You control the lifetime, an optional submission cap, and whether
the token remains active.

Tokens are managed from the **Invite links** panel inside the form builder
(`Admin → Forms → [form] → Invite links`).

---

## Requirements

- The form must be in **`published`** status before you can mint a token.
  Attempting to mint against a `draft` or `archived` form returns `409` with
  the message `cannot mint invite tokens for a form in status '<status>'`.
- The caller must hold the `forms:admin` scope and the `forms.publish` RBAC
  permission. Standard member tokens with `forms:read` can list tokens but
  cannot mint or revoke.

---

## Endpoints

### Mint a token

```
POST /api/v1/tenant/forms/definitions/:form_id/invites
```

**Request body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `tenant_id` | string | Yes | Tenant the form belongs to. |
| `expires_hours` | integer | No | Token lifetime in hours (minimum 1). Defaults to 720 (30 days). |
| `max_submissions` | integer or null | No | Hard cap on submissions. `null` or omitted means unlimited. |

**Success response — `201 Created`**

```json
{
  "id": "3f2e…",
  "tenant_id": "ten_…",
  "form_definition_id": "fd_…",
  "token": "a1b2c3d4-e5f6-…",
  "expires_at": "2026-06-16T12:00:00Z",
  "max_submissions": 100,
  "submission_count": 0,
  "created_at": "2026-05-16T12:00:00Z"
}
```

The `token` field is the raw UUID that makes up the share URL:

```
https://app.fastyoke.io/f/<token>
```

The token value is stored in the database only as a SHA-256 hash. If you lose
the URL you must mint a new token — there is no way to retrieve the plaintext
token after the `201` response.

---

### List tokens for a form

```
GET /api/v1/tenant/forms/definitions/:form_id/invites?tenant_id=<tenant_id>
```

Returns all tokens for the form (active, expired, and exhausted), ordered by
`created_at` descending. Requires the `forms:read` scope.

**Success response — `200 OK`**

An array of objects with the same shape as the mint response.
`submission_count` reflects the live counter — use it to track how many
submissions a token has received.

---

### Revoke a token

```
DELETE /api/v1/tenant/forms/invites/:invite_id?tenant_id=<tenant_id>
```

Deletes both the platform routing index and the tenant-side record. Any
holder who attempts to load or submit the form via the revoked URL receives
`410 Gone`. Existing submissions are **not** deleted — revocation prevents
future use only.

Requires the `forms:admin` scope and the `forms.delete` RBAC permission.

**Success response — `200 OK`**

```json
{ "revoked": true }
```

---

## Public URL behaviour

When a visitor opens `https://app.fastyoke.io/f/<token>`:

| Condition | HTTP status | Visitor sees |
|-----------|-------------|--------------|
| Token unknown | `404 Not Found` | Error page |
| Token revoked (row deleted) | `410 Gone` | "This invite has been revoked" |
| Token expired (`expires_at` in the past) | `410 Gone` | "This invite has expired or reached its submission limit" |
| Submission cap reached (`submission_count >= max_submissions`) | `410 Gone` | "This invite has expired or reached its submission limit" |
| Form unpublished after token was minted | `410 Gone` | "This form is no longer accepting submissions" |
| Token valid | `200 OK` | Form renders |

The response body also includes a `submissions_remaining` field (integer or
`null` for unlimited) so your frontend can display a "X responses remaining"
notice if desired.

### Submission atomicity

The submission counter is incremented atomically in the same `UPDATE` that
enforces the cap. If the increment touches zero rows (cap already reached or
token expired between the form load and the submit click), the submit returns
`410` without writing a `form_submissions` row.

---

## Tier limits

::callout{type="info" title="Solo caps"}
Solo invite tokens have a **30-day maximum lifetime** (720 hours) and a
**500-submission cap per token**. Attempting to mint with `expires_hours`
above 720 or `max_submissions` above 500 (or `null`) returns `402` with
error code `tier_quota_exceeded`. Upgrade to Pro for 1-year tokens and
unlimited submission caps.
::

| Limit | Solo | Pro | Team | Enterprise |
|-------|-------|-----|------|------------|
| Maximum token lifetime | 30 days | 1 year | 1 year | 1 year |
| Default lifetime pre-filled in UI | 7 days | 30 days | 30 days | 30 days |
| Maximum `max_submissions` per token | 500 | Unlimited | Unlimited | Unlimited |
| Default `max_submissions` pre-filled in UI | 50 | Blank (unlimited) | Blank (unlimited) | Blank (unlimited) |

When the request violates a tier cap the backend returns:

```json
HTTP 402 Payment Required
{
  "error": "tier_quota_exceeded",
  "tier": "hobby",
  "detail": {
    "feature": "forms_invite_long_lived",
    "limit_hours": 720,
    "requested_hours": 8760
  }
}
```

or, for the submissions dimension:

```json
{
  "error": "tier_quota_exceeded",
  "tier": "hobby",
  "detail": {
    "feature": "forms_invite_unlimited_submissions",
    "limit": 500,
    "requested_max_submissions": null
  }
}
```

See [Forms tier entitlements](/docs/forms/tiers) for the complete matrix.

---

## Error reference

| Status | Condition |
|--------|-----------|
| `400 Bad Request` | `tenant_id` is empty or missing. |
| `404 Not Found` | Form definition not found for `(form_id, tenant_id)`. |
| `409 Conflict` | Form is not in `published` status — see message for actual status. |
| `402 Payment Required` | `expires_hours` or `max_submissions` exceeds the tenant's tier cap. |
| `401 / 403` | Missing or insufficient JWT scope / RBAC permission. |
| `410 Gone` | (Public endpoint only) Token revoked, expired, or submission cap reached; or form was unpublished. |
