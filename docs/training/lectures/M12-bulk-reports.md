---
title: Lecture M12 — Bulk operations + Reports & dashboards
summary: The preview-and-commit contract, why cascade is opt-in per call, and how aggregates compute on demand.
order: 13
---

# Lecture M12 — Bulk operations + Reports & dashboards

## The mental model

Bulk operations are the escape hatch from "click-by-click."
They follow a strict **preview-then-commit** contract:
preview is read-only and returns a diff; commit is the
write. The two steps are linked by a short-lived
`session_id`. Dashboards aggregate the same data the
entity surface stores — no precomputed cache, no async
invalidation in v1.

## Key concepts

- **Preview is read-only.** Re-running it shows the same
  diff. Auditors verify by running preview twice and
  diffing the outputs.
- **Commit consumes the session.** `session_id` expires
  after ~15 minutes. Past expiry you re-preview.
- **Cascade is per-call.** Bulk-delete defaults to refusing
  if anything references the rows (409). Opt in with
  `cascade: true`; the response carries which referencing
  rows were affected.
- **Dashboards.** Composable widgets — bar, line, table —
  over an aggregate vocabulary (count, sum, avg, time
  bucket).
- **Digests.** A dashboard rendered + emailed on a
  schedule. Fields: `weekday` (Mon=0), `hour_utc` (UTC,
  not local), `recipients`.

## Common pitfalls

- **Committing past expiry.** You'll see a 410. Re-preview.
- **Cascade thinking.** Default-off catches mistakes; the
  reflex "always pass cascade: true" defeats the guard.
- **Local-time digest scheduling.** Everything is UTC.
  Schedule by your offset, not by your wall clock.

## Where to go next

- Lab: [M12 — Bulk ops & Reports](/docs/training/labs/M12-bulk-ops-reports).
- Reference: [/docs/bulk-operations](/docs/bulk-operations),
  [/docs/reports](/docs/reports).
