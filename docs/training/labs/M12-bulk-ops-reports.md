---
title: Lab M12 — Bulk ops & Reports
summary: Bulk-edit then bulk-delete with cascade via REST preview/commit, then compose a dashboard with bar and line widgets and schedule a weekly digest.
order: 24
---

# Lab M12 — Bulk ops & Reports

This lab pairs with **Module M12 — Bulk operations + Reports
& dashboards** in the [syllabus](/docs/training/syllabus).

## What you'll do

Use the bulk-edit `preview` + `commit` REST endpoints to tag
10+ vehicles. Use bulk-delete with `cascade` and inspect the
cascade report. Then open the admin shell and compose a
dashboard with one bar widget + one line widget. Schedule a
weekly digest to your own email.

## Before you begin

- A FastYoke tenant you're an admin of.
- A `fy_pat_` PAT with `data:write` scope (or an admin
  session JWT).
- At least 10+ `vehicle` records — if you don't have them,
  loop M3's CRUD curls.
- `curl` and `jq`.
- About 30 minutes.

## Or use Postman

If you prefer a GUI, run the REST portion (Steps 1–2) from
Postman instead of `curl`. Steps 3–5 are GUI work in the
admin shell — they stay in this lab page.

**Import once:** in Postman, choose **File → Import → Link**
and paste this URL:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

Click **Import**. Open the **Week 6 — Events, messaging, data
ops → M12 — Bulk ops & Reports** folder, set the `FY_TOKEN`
environment variable, then run **Step 1 — Preview** followed
by **Step 2 — Commit**. The Step 1 Tests tab captures the
`session_id` into the env so Step 2 can reference it.

The `curl` steps below remain the source of truth.

## Steps

1. **Preview a bulk edit.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"entity":"vehicle","filter":{"class":"Truck"},"patch":{"payload":{"audit_tag":"M12"}}}' \
     https://www.fastyoke.io/api/v1/tenant/bulk/preview
   ```

   **Checkpoint:** 200 with a diff body listing each row's
   id and the before/after value for the patched field.
   **Nothing has been written yet** — preview is read-only.
2. **Commit the bulk edit.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"session_id":"<from preview response>"}' \
     https://www.fastyoke.io/api/v1/tenant/bulk/commit
   ```

   **Checkpoint:** 200 with row-by-row outcomes.
   `GET /entities/vehicle?class=Truck` returns the records
   with `audit_tag: "M12"`.
3. **Bulk-delete with cascade.** Pick a small set (e.g., 2
   vehicles you don't need) and POST a bulk-delete with
   `cascade: true`.

   **Checkpoint:** the response carries a cascade report
   listing which referencing rows were soft-deleted. FSM
   job rows referencing the deleted vehicles retain their
   historical id (the same retention contract M3
   introduced).
4. **Compose a dashboard.** Open **Admin → Reports → New
   dashboard**. Add a **bar** widget showing vehicle count
   grouped by `class`. Add a **line** widget showing
   vehicle count grouped by week of `created_at`.

   **Checkpoint:** both widgets render with your data.
5. **Schedule a weekly digest.** Open **Reports → Digests
   → New digest**. Select the dashboard, set `weekday: 0`
   (Monday in the platform's convention), `hour_utc: 14`,
   recipients = your own email.

   **Checkpoint:** the digests list shows the new entry.
   The next Monday at 14:00 UTC the platform emails you a
   rendered snapshot.

## What you'll have at the end

A demonstrated bulk preview-then-commit cycle, a bulk-delete
whose cascade you observed, a dashboard with one bar + one
line widget, and a weekly digest scheduled to your inbox.

## Stuck?

See [Lab M12 — Solution](/docs/training/labs/M12-bulk-ops-reports-solution).

## Next

You've completed Week 6. Lab M13 ships in the Week 7 lab
pack PR. Return to the [syllabus](/docs/training/syllabus)
for the Week 7 reading list.
