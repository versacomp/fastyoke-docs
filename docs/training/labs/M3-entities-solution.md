---
title: Lab M3 — Solution
summary: Reference solution for Lab M3 — exact schema body, curl commands, expected JSON shapes per step, and a sample vehicle-crud.sh script.
order: 7
---

# Lab M3 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M3](/docs/training/labs/M3-entities). Work the lab
first — the friction of clicking through is intentional.
::

## Answer per step

### Step 1 — Author the schema

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{
    "slug": "vehicle",
    "fields": [
      {"name": "make",    "type": "string",  "required": true,  "label": "Make"},
      {"name": "class",   "type": "select",  "options": ["Compact","Mid-size","Truck"], "label": "Vehicle class"},
      {"name": "mileage", "type": "integer", "min": 0,          "label": "Mileage (km)"}
    ]
  }' \
  https://www.fastyoke.io/api/v1/tenant/schemas
```

Expected: `HTTP/2 200` and a body echoing the schema with
the three annotated fields.

### Step 2 — Create a record (idempotent)

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: csv-acme-001" \
  -d '{"payload":{"make":"Acme","class":"Truck","mileage":12000}}' \
  https://www.fastyoke.io/api/v1/tenant/entities/vehicle
```

Expected: `HTTP/2 200` with `{"id":"...","payload":{...}}`.
Re-running the same request with the same `Idempotency-Key`
returns the same `id`.

### Step 3 — PATCH

```bash
curl -i -X PATCH \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"payload":{"mileage":13500}}' \
  https://www.fastyoke.io/api/v1/tenant/entities/vehicle/$ID
```

Expected: `HTTP/2 200` with the new mileage in the
response.

### Step 4 — LIST

```bash
curl -i -H "Authorization: Bearer $FY_PAT" \
  "https://www.fastyoke.io/api/v1/tenant/entities/vehicle?limit=10"
```

Expected: `HTTP/2 200` with a paginated body containing
at least one record whose `id` matches step 2's response.

### Step 5 — Transition

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"event":"<your-event-name>"}' \
  https://www.fastyoke.io/api/v1/tenant/jobs/$ID/transition
```

Expected: `HTTP/2 200`. The `event_log` row for the
resulting job carries the record id as its source.

### Step 6 — DELETE + the FSM-job retention contract

```bash
curl -i -X DELETE \
  -H "Authorization: Bearer $FY_PAT" \
  https://www.fastyoke.io/api/v1/tenant/entities/vehicle/$ID
```

Expected: `HTTP/2 204`. A subsequent GET returns `HTTP/2
404`. **However**, the FSM job row from step 5 still
references the deleted entity id in `event_log`. The job
log is append-only by design — deleting an entity does
not erase its history. This is the load-bearing learning
of step 6.

## A sample `vehicle-crud.sh`

```bash
#!/usr/bin/env bash
set -e

# 1) Author the schema (idempotent at the slug level)
curl -fsS -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d @schemas/vehicle.json \
  https://www.fastyoke.io/api/v1/tenant/schemas

# 2) Create a record (idempotent at the Idempotency-Key)
ID=$(curl -fsS -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: csv-acme-001" \
  -d '{"payload":{"make":"Acme","class":"Truck","mileage":12000}}' \
  https://www.fastyoke.io/api/v1/tenant/entities/vehicle \
  | jq -r .id)

# 3) PATCH
curl -fsS -X PATCH \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"payload":{"mileage":13500}}' \
  https://www.fastyoke.io/api/v1/tenant/entities/vehicle/$ID

# 4) LIST
curl -fsS -H "Authorization: Bearer $FY_PAT" \
  "https://www.fastyoke.io/api/v1/tenant/entities/vehicle?limit=10"

# 5) DELETE
curl -fsS -X DELETE \
  -H "Authorization: Bearer $FY_PAT" \
  https://www.fastyoke.io/api/v1/tenant/entities/vehicle/$ID
```

If yours looks meaningfully like this, you've done the lab.

## Back to the lab

[Lab M3 — Author a vehicle entity](/docs/training/labs/M3-entities).
