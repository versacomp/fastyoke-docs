---
title: Bulk operations
summary: Run the same edit, delete, or FSM transition against many entity records at once. Admin-only. Always two-step — preview the change, review what would happen, then commit (or cancel).
order: 1
---

# Bulk operations

Bulk operations let you run the same change against many entity
records at once. Three operations ship today — **Edit** records,
**Delete** records (with cascade), and fire an FSM **Transition**
on every record. Every bulk operation is admin-only, and every one
is two-step: you preview the change, the platform tells you
exactly what would happen row-by-row, and then you commit (or
cancel).

## Three operations

- **Edit** — set one or more fields to new values on every
  selected record.
- **Delete** — hard-delete the records. Cascades to their FSM
  jobs and to the event-log rows that referenced them.
- **Transition** — fire the same FSM event on every selected
  record. Each record's guard evaluates independently — some rows
  may succeed and others may surface a `guard_failed` outcome.

## How you select records

Two selection modes:

- **By id (`ids`)** — explicit list of record ids. Use this when
  you've checkboxed rows in the EntityListRenderer.
- **By filter (`filter`)** — a `filter_expr` string plus a
  `count_at_preview` snapshot. Use this when you want to bulk-apply
  to every record matching a filter. The platform re-counts at
  commit time; rows that have changed since preview surface as
  `selection_drift` per-row.

## The preview-commit lifecycle

Every bulk operation runs through a five-state session: the
platform first computes a preview (`previewed`), then on commit
moves the session through `committing` to either `committed` or
`errored`. You can also `cancel` from `previewed`.

The preview tells you `total_resolved`, `would_succeed`,
`would_skip`, `would_error`, a sample of affected rows, and the
list of per-row errors. **Read the preview before committing** —
this is the only opportunity to catch mistakes before the change
applies.

Commits under 1000 rows run synchronously on the request and
return when done. Commits at or above 1000 rows enqueue on the
cron worker; the response carries the session id and you poll
`GET /sessions/:id` until status reaches `committed` or `errored`.

## Per-row outcomes

Each row in a bulk operation gets one outcome code. The session
continues past per-row errors — `succeeded`, `skipped`, and
`errored` counts are recorded, and you can download `errors.csv`
for the full per-row report.

The eight codes are documented in the
[API reference](/docs/bulk-operations/api-reference). The two
that surface most often:

- `guard_failed` — for transitions, the FSM guard returned false
  for this record.
- `selection_drift` — for filter-mode operations, the row didn't
  match the filter at commit time even though it did at preview.

## Hard-delete cascade

When you bulk-delete entity records, the platform hard-deletes
the records AND every FSM job that referenced them AND every
event-log row that referenced those jobs. The preview surfaces
the cascade as a `CascadeSummary` reporting `jobs_to_delete` and
`event_log_rows_added` — review these numbers before committing.

**Hard-delete is permanent** — there's no rollback, no undo, and
no recovery once a delete commits.

## Who can run bulk operations

**Admin role only.** Every bulk-operations endpoint rejects
non-admin callers with 403. There's no per-permission gate beyond
admin; admin is the gate.

There are **no tier caps** on bulk operations — every tier that
has admin users can run them.

## What's not bulk operations today

Documentary list of explicit non-features today:

- **Bulk insert** — only edit, delete, and transition ship today.
  Creating many records at once isn't supported.
- **Cross-entity operations** — a session targets one entity at
  a time. You can't bulk-edit records across two different entity
  types in one operation.
- **Scheduled commits** — commit fires immediately or on the next
  cron tick (for async). There's no "run this tomorrow at 3am."
- **Rollback** of a committed session.
- **Approval** / two-party authorization before commit.
- **Resume from partial failure** — a session that errors stops
  where it stopped; re-run with a corrected selection.

## See also

- [API reference](/docs/bulk-operations/api-reference) — REST
  endpoints, session lifecycle states, and the full set of
  per-row outcome codes.
- [Entities](/docs/entities) — where you select records from.
- [Jobs](/docs/jobs) — for bulk transitions and the event log.
