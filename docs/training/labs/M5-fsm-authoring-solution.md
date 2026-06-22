---
title: Lab M5 — Solution
summary: Reference solution for Lab M5 — exact schema body with three JSONLogic guards, sample 422 + 200 transition responses, and a sample hand-trace.
order: 11
---

# Lab M5 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M5](/docs/training/labs/M5-fsm-authoring). Work the
lab first — the friction of clicking through is
intentional.
::

## Answer per step

### Step 1 — Author the schema

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{
    "slug": "service_ticket",
    "initial_state": "submitted",
    "states": ["submitted","triaged","in_progress","awaiting_customer","resolved"],
    "transitions": [
      {"event":"submit_review",  "from":"submitted",         "to":"triaged"},
      {"event":"start_work",     "from":"triaged",           "to":"in_progress",
       "guard": {">=": [{"var": "priority"}, 2]}},
      {"event":"await_customer", "from":"in_progress",       "to":"awaiting_customer",
       "guard": {"!=": [{"var": "assignee"}, null]}},
      {"event":"resolve",        "from":"awaiting_customer", "to":"resolved",
       "guard": {"==": [{"var": "resolution"}, "fixed"]}}
    ]
  }' \
  https://www.fastyoke.io/api/v1/tenant/schemas
```

Expected: `HTTP/2 200` with the full schema echoed back.

### Step 2 — Refused first guard

With a record at `priority: 1`:

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"event":"start_work"}' \
  https://www.fastyoke.io/api/v1/tenant/jobs/$ID/transition
```

Expected:

```
HTTP/2 422
content-type: application/json

{"error":"guard_refused","event":"start_work","reason":"guard evaluated to false"}
```

### Steps 3–6 — Successful transitions

PATCH the relevant payload field, re-fire. Each `200`
response carries the new job state in the body. The
sequence:

| Step | PATCH | Event | Expected new state |
|------|-------|-------|--------------------|
| 3 | `priority: 2` | `start_work` | `in_progress` |
| 5 | `assignee: "you@example.com"` | `await_customer` | `awaiting_customer` |
| 6 | `resolution: "fixed"` | `resolve` | `resolved` |

### Step 7 — Sample hand-trace

> A new service ticket arrives at `priority: 1`. The
> `submit_review` transition fires unconditionally (no
> guard) — state advances to `triaged`. An attempt to fire
> `start_work` next is refused with 422 because the
> priority guard `priority >= 2` evaluates to false. We
> PATCH `priority` to 2 and re-fire; the guard now passes
> and the state advances to `in_progress`. We attempt
> `await_customer` without an assignee — refused. We
> PATCH `assignee` and fire — state advances to
> `awaiting_customer`. We PATCH `resolution: "fixed"` and
> fire `resolve`; the third guard passes and the state
> advances to `resolved`. End-state reached after three
> guard evaluations, one initial refusal each on guards 1
> and 2, and a one-shot pass on guard 3.

If your trace names all three guards by purpose and
identifies the first 422, you've done the lab.

## Why JSONLogic and not a host function?

Guards are evaluated in a **sandbox**. No host syscalls,
no time, no random, no I/O. The same payload + the same
guard expression always produces the same result.
That's the load-bearing property — replays during
recovery, audits, or migrations all evaluate guards
deterministically. A host function couldn't make that
guarantee.

## Back to the lab

[Lab M5 — FSM authoring](/docs/training/labs/M5-fsm-authoring).
