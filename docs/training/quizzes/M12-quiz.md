---
title: Quiz M12 — Bulk operations + Reports & dashboards
summary: 10 multiple-choice questions on preview-and-commit, cascade, dashboard aggregates, and digest scheduling.
order: 13
---

# Quiz M12 — Bulk operations + Reports & dashboards

Pairs with [Lecture M12](/docs/training/lectures/M12-bulk-reports)
and [Lab M12](/docs/training/labs/M12-bulk-ops-reports).

**Q1.** What does the preview-and-commit contract guarantee?

a) Preview writes; commit confirms
b) Preview is read-only; commit is the only write
c) Both write; commit is faster
d) Preview is a dry-run for cost estimation only

<details>
<summary>Answer + why</summary>

**b)** Re-running preview shows the same diff. Auditors
verify by diffing two previews.

</details>

**Q2.** How are aggregates evaluated?

a) Precomputed nightly
b) On demand from posted rows
c) Cached in Redis
d) Estimated via sampling

<details>
<summary>Answer + why</summary>

**b)** No async invalidation in v1 — refresh the widget
if it looks stale.

</details>

**Q3.** Why does a digest carry a UTC hour and not a timezone?

a) Simpler implementation
b) Avoids DST ambiguity and tenant-mixing edge cases
c) Email delivery is UTC anyway
d) Bug — it should carry a timezone

<details>
<summary>Answer + why</summary>

**b)** UTC removes ambiguity. Convert at the recipient if
you want local time.

</details>

**Q4.** Cascade is…

a) On by default
b) Opt-in per call (`cascade: true`)
c) Configured per tenant
d) Configured per entity

<details>
<summary>Answer + why</summary>

**b)** Default-off catches mistakes; opt in when you
mean it.

</details>

**Q5.** What does a `session_id` from preview expire after?

a) 1 minute
b) ~15 minutes
c) 24h
d) Never

<details>
<summary>Answer + why</summary>

**b)** Short window. Past expiry, re-preview.

</details>

**Q6.** What happens to FSM job rows referencing a bulk-deleted entity row (with cascade)?

a) Hard-deleted
b) Soft-deleted; the cascade report lists them
c) Unchanged
d) Returns 409

<details>
<summary>Answer + why</summary>

**b)** Soft-delete preserves the audit chain; the report
shows what cascaded.

</details>

**Q7.** What's `weekday: 0` in the digest scheduler?

a) Sunday
b) Monday
c) Configurable per tenant
d) Saturday

<details>
<summary>Answer + why</summary>

**b)** Mon=0 in the platform's convention — not cron's
Sun=0.

</details>

**Q8.** Committing past `session_id` expiry returns…

a) 200 with idempotency
b) 410 (gone)
c) 200 but no-op
d) 500

<details>
<summary>Answer + why</summary>

**b)** Session has expired. Re-preview to get a fresh
session.

</details>

**Q9.** Bulk-delete without `cascade: true` against rows that have referencers returns…

a) 200 with cascade
b) 409 (conflict)
c) Deletes and orphans the referencers
d) Soft-deletes

<details>
<summary>Answer + why</summary>

**b)** Default-off requires you to opt in.

</details>

**Q10.** Why does the platform refresh dashboard widgets on demand only?

a) Bug
b) Avoids the async invalidation surface in v1
c) Faster
d) Saves bandwidth

<details>
<summary>Answer + why</summary>

**b)** No background invalidator means no stale-cache bug
class.

</details>
