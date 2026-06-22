---
title: Lab M5 — FSM authoring
summary: Author a 5-state service-ticket FSM with three JSONLogic guards. POST it. Attempt a transition the guard should refuse and observe the 422. Hand-trace one full run from submitted through resolved.
order: 10
---

# Lab M5 — FSM authoring

This lab pairs with **Module M5 — FSM authoring** in the
[syllabus](/docs/training/syllabus).

## What you'll do

Author a 5-state service-ticket lifecycle with three
JSONLogic guards on transitions. POST the schema. Attempt
a transition the first guard should refuse and observe the
422. PATCH the record so the guard passes, fire the
transition. Repeat for the other two guards. Hand-trace
the full run.

## Before you begin

- A FastYoke tenant you're an admin of.
- A `fy_pat_` PAT with `workflow:admin` scope, OR the
  one-hour test token from the M2 lab, OR an admin session.
- `curl`.
- About 45 minutes.

## Or use Postman

If you prefer a GUI, run the lab steps from Postman instead
of `curl`. Each request has a **Tests** tab with the same
checkpoint assertions the `curl` lab pins.

**Import once:** in Postman, choose **File → Import → Link**
and paste this URL:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

Click **Import**. Open the **Week 3 — Core primitives II → M5
— FSM authoring** folder, set the `FY_TOKEN` and `FY_ID`
environment variables, then run each request in order. The
422 refusal in Step 2 is **expected** — the Tests tab
asserts a 422 status. Green checks mean the lab is working.

The `curl` steps below remain the source of truth.

## Steps

1. **Author the schema** with five states and four
   transitions:
   - States: `submitted`, `triaged`, `in_progress`,
     `awaiting_customer`, `resolved`.
   - `submit_review`: `submitted → triaged`. No guard.
   - `start_work`: `triaged → in_progress`. **Guard:**
     `{ ">=": [{ "var": "priority" }, 2] }` — only fires
     when `priority` is at least 2.
   - `await_customer`: `in_progress → awaiting_customer`.
     **Guard:** `{ "!=": [{ "var": "assignee" }, null] }`
     — only fires when `assignee` is set.
   - `resolve`: `awaiting_customer → resolved`. **Guard:**
     `{ "==": [{ "var": "resolution" }, "fixed"] }` —
     only fires when the payload's `resolution` is the
     literal `"fixed"`.

   POST it to `/api/v1/tenant/schemas`.

   **Checkpoint:** `GET /schemas/service_ticket` returns
   200 with all five states and three guards.
2. **Create a record + job** with `priority: 1` (low).
   Fire `submit_review`, then try `start_work`.

   **Checkpoint:** `start_work` returns **422
   Unprocessable Entity** with a guard-refusal body — the
   priority is below the threshold.
3. **PATCH the record to `priority: 2`** and re-fire
   `start_work`.

   **Checkpoint:** the transition now returns 200; the
   job state advances to `in_progress`.
4. **Attempt `await_customer` without setting
   `assignee`.**

   **Checkpoint:** 422 from the second guard.
5. **PATCH `assignee`, fire `await_customer`.**

   **Checkpoint:** 200; state advances to
   `awaiting_customer`.
6. **PATCH `resolution: "fixed"`, fire `resolve`.**

   **Checkpoint:** 200; state is now `resolved`.
7. **Hand-trace the run.** Write a one-page outline:
   payload values at each step, which guard each
   transition evaluated, which one refused first and why.

   **Checkpoint:** your trace names all three guards by
   purpose and identifies the first 422.

## What you'll have at the end

A `service-ticket.json` schema file and a one-page
hand-traced sample run showing the three guard
evaluations and the first 422.

## Stuck?

See [Lab M5 — Solution](/docs/training/labs/M5-fsm-authoring-solution).

## Next

[Lab M6 — Jobs](/docs/training/labs/M6-jobs).
