---
title: Lab M6 — Solution
summary: Reference solution for Lab M6 — exact PATCH that adds the self-loop, exact admin-cancel curl, sample event_log row JSON, and a sample job-lifecycle.sh script.
order: 13
---

# Lab M6 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M6](/docs/training/labs/M6-jobs). Work the lab
first — the friction of clicking through is intentional.
::

## Answer per step

### Step 2 — Add the self-loop transition

PATCH the schema to append the `check_in` transition:

```bash
curl -i -X PATCH \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{
    "transitions": [
      {"event":"submit_review",  "from":"submitted",         "to":"triaged"},
      {"event":"start_work",     "from":"triaged",           "to":"in_progress",
       "guard": {">=": [{"var": "priority"}, 2]}},
      {"event":"await_customer", "from":"in_progress",       "to":"awaiting_customer",
       "guard": {"!=": [{"var": "assignee"}, null]}},
      {"event":"resolve",        "from":"awaiting_customer", "to":"resolved",
       "guard": {"==": [{"var": "resolution"}, "fixed"]}},
      {"event":"check_in",       "from":"in_progress",       "to":"in_progress"}
    ]
  }' \
  https://www.fastyoke.io/api/v1/tenant/schemas/service_ticket
```

Expected `HTTP/2 200` with the updated schema.

### Step 4 — Self-loop firing + event_log row

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"event":"check_in"}' \
  https://www.fastyoke.io/api/v1/tenant/jobs/$ID/transition
```

Expected `HTTP/2 200`. The job's state remains
`in_progress`. The resulting `event_log` row:

```json
{
  "id": "evt_…",
  "job_id": "job_…",
  "event_type": "check_in",
  "from_state": "in_progress",
  "to_state": "in_progress",
  "occurred_at": "2026-06-22T…"
}
```

### Step 5 — Admin cancel override

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"target_state":"resolved","reason":"learner override for the M6 lab"}' \
  https://www.fastyoke.io/api/v1/tenant/jobs/$ID/cancel
```

Expected `HTTP/2 200`. The job's state is now `resolved`.

### Step 6 — Override event_log row

```json
{
  "id": "evt_…",
  "job_id": "job_…",
  "event_type": "__admin_cancel__",
  "from_state": "in_progress",
  "to_state": "resolved",
  "actor": "you@example.com",
  "reason": "learner override for the M6 lab",
  "occurred_at": "2026-06-22T…"
}
```

## The override contract

The admin cancel endpoint is an **out-of-band**
administrative escape hatch. It:

- Bypasses every guard on the transition graph.
- Requires a non-empty `reason` (the audit-trail
  contract).
- Records `actor` (the operator email) on the event_log
  row.
- Is **never** used to model a normal lifecycle path —
  that's what FSM transitions are for.

See `/docs/jobs` for the platform-side override
contract.

## Sample `job-lifecycle.sh`

```bash
#!/usr/bin/env bash
set -e

# 1) submit_review (no guard)
curl -fsS -X POST -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"event":"submit_review"}' \
  https://www.fastyoke.io/api/v1/tenant/jobs/$ID/transition

# 2) start_work (priority guard, after PATCHing priority to 2)
curl -fsS -X PATCH -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"payload":{"priority":2}}' \
  https://www.fastyoke.io/api/v1/tenant/entities/service_ticket/$REC_ID

curl -fsS -X POST -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"event":"start_work"}' \
  https://www.fastyoke.io/api/v1/tenant/jobs/$ID/transition

# 3) check_in self-loop
curl -fsS -X POST -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"event":"check_in"}' \
  https://www.fastyoke.io/api/v1/tenant/jobs/$ID/transition

# 4) admin override to resolved (bypasses the two remaining guards)
curl -fsS -X POST -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"target_state":"resolved","reason":"learner override for the M6 lab"}' \
  https://www.fastyoke.io/api/v1/tenant/jobs/$ID/cancel

# 5) inspect the event_log
curl -fsS -H "Authorization: Bearer $FY_PAT" \
  https://www.fastyoke.io/api/v1/tenant/jobs/$ID/events | jq .
```

If yours looks meaningfully like this, you've done the
lab.

## Back to the lab

[Lab M6 — Jobs](/docs/training/labs/M6-jobs).
