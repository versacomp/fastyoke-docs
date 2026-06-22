---
title: Lab M3 — Author a vehicle entity
summary: Mint a fy_pat_ token, POST a vehicle schema with three annotated fields, exercise the full REST surface (POST, GET, PATCH, LIST, DELETE), and observe what happens to referencing FSM job rows on delete. End with a vehicle-crud.sh script that re-runs cleanly.
order: 6
---

# Lab M3 — Author a vehicle entity

This lab pairs with **Module M3 — Entities** in the
[syllabus](/docs/training/syllabus).

## What you'll do

Mint a `fy_pat_` token with `data:write` scope. POST a
schema for the `vehicle` entity with three field-level
annotations (a label, a single-select options list, and a
numeric constraint). Exercise the full REST surface — POST
a record, GET by id, GET list, PATCH a field, DELETE —
then inspect what happens to FSM job rows that referenced
the deleted record.

## Before you begin

- A FastYoke tenant you're an admin of.
- A `fy_pat_` PAT with `data:write` scope. The
  [M2 lab](/docs/training/labs/M2-token-walkthrough) shows
  how to mint one.
- `curl` on your shell.
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

Click **Import**. Open the **Week 2 — Core primitives I → M3
— Author a vehicle entity** folder, set the `FY_TOKEN`
environment variable, then run each request in order. The
pre-request scripts capture the record id into `FY_ID` so
later steps find it automatically.

The `curl` steps below remain the source of truth.

## Steps

1. **Author the `vehicle` schema** with three annotated
   fields:
   - `make` — label "Make", required string.
   - `class` — label "Vehicle class", single-select with
     options `Compact`, `Mid-size`, `Truck`.
   - `mileage` — label "Mileage (km)", non-negative
     integer.

   POST it to `/api/v1/tenant/schemas`.

   **Checkpoint:** `curl -i -H "Authorization: Bearer $FY_PAT" .../api/v1/tenant/schemas/vehicle` returns 200 with all three fields and their annotations.
2. **Create a vehicle record** with one valid payload.
   POST to `/api/v1/tenant/entities/vehicle` with an
   `Idempotency-Key` header so a re-run is safe.

   **Checkpoint:** response carries an `id`; `GET
   /entities/vehicle/{id}` round-trips the payload.
3. **PATCH the record.** Change `mileage` to a new value.

   **Checkpoint:** PATCH returns 200; subsequent GET
   shows the new value.
4. **List the entity.** `GET /entities/vehicle?limit=10`.

   **Checkpoint:** the list contains at least one entry
   whose `id` matches what step 2 returned.
5. **Trigger a workflow against the record.** Use the
   `vehicle_lifecycle` schema (if pre-installed) or a
   custom FSM you author here. Fire one transition.

   **Checkpoint:** `event_log` for the resulting job
   names the record id.
6. **DELETE the record.**

   **Checkpoint:** subsequent `GET /entities/vehicle/{id}`
   returns 404; the FSM job row from step 5 retains the
   record id as a historical reference (that's the
   "what happens on delete" learning — the job log is
   append-only).

## What you'll have at the end

A `vehicle-crud.sh` script demonstrating the five
operations (schema POST + record POST + PATCH + LIST +
DELETE) plus a one-line note documenting how delete
interacts with referencing FSM job rows.

## Stuck?

See [Lab M3 — Solution](/docs/training/labs/M3-entities-solution).

## Next

[Lab M4 — Public-link form](/docs/training/labs/M4-forms).
