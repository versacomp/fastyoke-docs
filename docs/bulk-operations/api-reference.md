---
title: Bulk operations API reference
summary: REST endpoints for creating a preview, listing sessions, committing or cancelling — plus the five session statuses, the eight per-row outcome codes, and the sync-versus-async dispatch threshold.
order: 2
---

# Bulk operations API reference

This page covers the six REST endpoints, the session-status state
machine, and the per-row outcome codes that bulk operations
expose. The same surface is available to any client with a
tenant-scoped admin JWT.

## Authentication

Every endpoint below requires:

- A **tenant-scoped JWT** — the same token the rest of the
  platform uses. See [Authentication](/docs/auth) for how to mint
  and present one.
- **Admin role.** Non-admin callers receive 403 on every
  endpoint. There's no per-permission gate beyond admin.

## Create a preview

```http
POST /api/v1/tenant/bulk-ops/preview
Content-Type: application/json
Authorization: Bearer <admin tenant JWT>
```

Request body:

```json
{
  "entity_name": "customer",
  "operation": "edit",
  "selection": { "kind": "ids", "ids": ["rec_…", "rec_…"] },
  "op_params": { "kind": "edit", "fields": [{ "field_key": "status", "new_value": "active" }] }
}
```

`selection.kind` is either `"ids"` (with `ids: [...]`) or
`"filter"` (with `filter_expr` and `count_at_preview`). `op_params.kind`
matches `operation` — `"edit"` (with `fields`), `"delete"` (no
payload), or `"transition"` (with `event_name`).

Returns the session with `status: "previewed"` and a full
`BulkOpPreview` — `total_resolved`, `would_succeed`,
`would_skip`, `would_error`, optional `cascade_summary`, an
array of per-row `errors`, and a `sample_affected` list of rows
that would be affected.

## List sessions

```http
GET /api/v1/tenant/bulk-ops/sessions
Authorization: Bearer <admin tenant JWT>
```

Returns sessions ordered newest first.

## Get one session

```http
GET /api/v1/tenant/bulk-ops/sessions/:id
Authorization: Bearer <admin tenant JWT>
```

Returns the full session — including `preview` and, after commit,
`result` (with `succeeded`, `skipped`, `errored`, optional
`cascade_applied`, and `committed_at`).

## Commit

```http
POST /api/v1/tenant/bulk-ops/sessions/:id/commit
Authorization: Bearer <admin tenant JWT>
```

Transitions the session through `committing` and either to
`committed` or `errored`. **Per-row errors don't cause `errored`**
— the session continues and records them. `errored` is reserved
for infrastructure failures that aborted the commit before it
could finish processing rows.

## Cancel

```http
POST /api/v1/tenant/bulk-ops/sessions/:id/cancel
Authorization: Bearer <admin tenant JWT>
```

Only valid from `previewed`. Moves the session to `cancelled`.

## Download errors as CSV

```http
GET /api/v1/tenant/bulk-ops/sessions/:id/errors.csv
Authorization: Bearer <admin tenant JWT>
```

Returns `text/csv` with one row per per-row error:
`record_id,current_state,code,message`. The same data is
available in the preview's `errors` array in JSON; this endpoint
exists for direct spreadsheet ingestion.

## Session statuses

The session's `status` field is one of five values:

| Status | Meaning |
|--------|---------|
| `previewed` | Session created; preview computed; not yet committed. |
| `committing` | Commit is in flight. Synchronous path for sessions under 1000 rows; async on cron at or above. |
| `committed` | Commit finished; `result` populated. |
| `errored` | Infrastructure failure during commit. Per-row errors do NOT cause this — they're recorded in `result.errored`. |
| `cancelled` | User cancelled before commit. Only reachable from `previewed`. |

## Per-row outcome codes

Each row in a bulk operation gets one outcome code. The eight
codes are the complete vocabulary today; client code that
branches on `code` can rely on this set being closed.

| Code | Meaning |
|------|---------|
| `record_not_found` | The row was selected by id but no longer exists. |
| `invalid_fsm_from_state` | A transition can't fire because the record's current state has no matching outbound edge. |
| `guard_failed` | A transition's guard evaluated false for this record. |
| `missing_required` | An edit would leave a required field empty. |
| `type_mismatch` | An edit value is the wrong type for the field. |
| `unknown_enum` | An edit value isn't one of the field's enum options. |
| `unknown_field` | An edit references a field that doesn't exist on this entity. |
| `selection_drift` | Filter-mode `count_at_preview` didn't match commit-time count; this row didn't match the filter at commit. |

## Threshold and dispatch

`POST /commit` dispatches synchronously when
`total_resolved < 1000` — the request returns when the commit
finishes. At `1000` rows or more, the commit enqueues on the
cron worker and the response returns immediately with the
session id.

For async commits, poll `GET /sessions/:id` until
`status == "committed" || "errored"`. The session's
`result` field is populated once commit reaches a terminal
state.

## Errors

Standard `AppError` envelope used elsewhere on the platform.
The notable ones for bulk operations:

- `403` — non-admin caller.
- `404` — unknown session id, or session belongs to another
  tenant.
- `409` — invalid status transition (e.g., commit on an already-
  `cancelled` session, or cancel on a `committed` session).

See [Error envelope](/docs/developers/errors) for the full
status-code table.
