---
title: Lab M6 — Jobs
summary: Drive a job through the M5 schema, add a self-loop transition for an audit event, fire the admin cancel override, inspect both event_log rows (self-loop + override).
order: 12
---

# Lab M6 — Jobs

This lab pairs with **Module M6 — Jobs** in the
[syllabus](/docs/training/syllabus).

## What you'll do

Take the `service_ticket` schema from M5. Drive one job
through two transitions. Add a **self-loop** transition
for an audit event (`check_in` on `in_progress`). Fire it.
Then use the admin cancel override endpoint to force the
job to a terminal state, bypassing the remaining guards.
Inspect both `event_log` rows.

## Before you begin

- A FastYoke tenant you're an admin of (admin role
  required for the override).
- An admin session JWT (or the M2 test token).
- The `service_ticket` schema from M5 — or any FSM with at
  least one state.
- `curl` + `jq`.
- About 30 minutes.

## Or use Postman

If you prefer a GUI, run the lab steps from Postman instead
of `curl`. Each request has a **Tests** tab with the same
checkpoint assertions the `curl` lab pins.

**Import once:** in Postman, choose **File → Import → Link**
and paste this URL:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

Click **Import**. Open the **Week 3 — Core primitives II → M6
— Jobs** folder, set the `FY_TOKEN` and `FY_ID` environment
variables, then run each request in order. Step 6's Tests
tab asserts the `event_log` response contains the
`__admin_cancel__` event type — that confirms the override
landed correctly.

The `curl` steps below remain the source of truth.

## Steps

1. **Create a service-ticket record + job.** Fire
   `submit_review`.

   **Checkpoint:** `GET /jobs/$ID` shows the job in
   `triaged`.
2. **Add a self-loop transition to the schema.** PATCH
   the schema to append a `check_in` transition where
   `from == to == "in_progress"`. No guard.

   **Checkpoint:** the updated schema lists the
   self-loop.
3. **Advance to `in_progress`.** PATCH the record's
   `priority` to 2 and fire `start_work`.

   **Checkpoint:** state is `in_progress`.
4. **Fire the self-loop.** `POST /jobs/$ID/transition`
   with `event: "check_in"`.

   **Checkpoint:** 200; `GET /jobs/$ID` still shows
   `in_progress` (state unchanged); a new `event_log`
   row exists for the firing.
5. **Use the admin cancel override.** `POST
   /api/v1/tenant/jobs/$ID/cancel` with body
   `{"target_state":"resolved","reason":"learner override
   for the M6 lab"}`.

   **Checkpoint:** 200; `GET /jobs/$ID` shows `resolved`;
   the override bypassed both remaining guards.
6. **Inspect the event_log.** Fetch `GET /jobs/$ID/events`.
   Find the self-loop row and the override row.

   **Checkpoint:** the self-loop row carries the
   transition's event name (`check_in`); the override
   row carries `event_type: "__admin_cancel__"`, your
   `reason`, and the operator email as `actor`.

## What you'll have at the end

A `job-lifecycle.sh` script plus a JSON paste of the two
key `event_log` rows showing the self-loop firing and
the admin override.

## Stuck?

See [Lab M6 — Solution](/docs/training/labs/M6-jobs-solution).

## Next

You've completed Week 3. Lab M7 ships in the Week 4 lab
pack PR. Return to the
[syllabus](/docs/training/syllabus) for the Week 4
reading list.
