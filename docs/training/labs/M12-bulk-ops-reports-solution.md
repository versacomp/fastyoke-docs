---
title: Lab M12 — Solution
summary: Answer key for the M12 bulk ops & reports lab — exact preview/commit/cascade curls, dashboard composer menu paths, digest scheduling fields, and the common gotchas.
order: 25
---

# Lab M12 — Solution

> **Reveal warning.** This page is the **answer key** for
> [Lab M12 — Bulk ops & Reports](/docs/training/labs/M12-bulk-ops-reports).
> Try each step on your own first; the friction is where the
> learning lives.

## Step 1 — Preview response shape

```json
{
  "session_id": "blk_abc123",
  "entity": "vehicle",
  "matched": 12,
  "diff": [
    { "id": "veh_001", "payload.audit_tag": [null, "M12"] },
    { "id": "veh_002", "payload.audit_tag": [null, "M12"] }
  ],
  "expires_at": "2026-06-22T15:00:00Z"
}
```

The diff arrays are `[before, after]`. `session_id` is your
handle for the commit — it expires after about 15 minutes.

## Step 2 — Commit

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"session_id":"blk_abc123"}' \
  https://www.fastyoke.io/api/v1/tenant/bulk/commit
```

Response:

```json
{
  "session_id": "blk_abc123",
  "written": 12,
  "failed": 0,
  "rows": [ { "id": "veh_001", "status": "written" } ]
}
```

## Step 3 — Cascade

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"entity":"vehicle","ids":["veh_001","veh_002"],"cascade":true}' \
  https://www.fastyoke.io/api/v1/tenant/bulk/delete
```

Response:

```json
{
  "deleted": 2,
  "cascade": [
    { "table": "service_job", "rows_affected": 5, "mode": "soft-delete" }
  ]
}
```

`cascade: true` opts in per-call — leave it off and the
platform returns 409 if anything references the rows.

## Step 4 — Dashboard composer menu paths

- **Admin → Reports → New dashboard.**
- **Add widget → Bar.** Source: entity `vehicle`. Group by:
  `class`. Aggregate: count.
- **Add widget → Line.** Source: entity `vehicle`. Time
  bucket: week. X: `created_at`. Aggregate: count.

## Step 5 — Digest scheduling fields

| Field        | Value           | Note                                          |
| ------------ | --------------- | --------------------------------------------- |
| `weekday`    | `0`             | Monday in the platform's convention (Mon=0, not Sun=0). |
| `hour_utc`   | `14`            | UTC, **not** your local time.                 |
| `recipients` | `[your_email]`  | Comma-list supported.                         |

## Three common gotchas

- **Preview is read-only.** Re-running it shows the same
  diff — the write is the `commit`.
- **Cascade is per-call.** Off by default; bulk-delete
  returns 409 if anything references the rows and you
  forgot to opt in.
- **Dashboard aggregates compute on demand.** If a widget
  looks stale right after a bulk edit, refresh it — there's
  no async invalidation in v1.

## Back to the lab

Return to [Lab M12 — Bulk ops & Reports](/docs/training/labs/M12-bulk-ops-reports).
