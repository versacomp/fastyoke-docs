---
title: Jobs
summary: The runtime side of workflows — live FSM instances you fire transitions against.
order: 4
---

# Jobs

A **job** is a running instance of an [FSM schema](/docs/workflows).
Where the Workflows section covers authoring the graph, this
section is about what happens after you press play: spawning jobs,
firing transitions, reading the audit trail, and overriding when
reality diverges from the workflow model.

::section-cards{section="jobs"}
::

## Lifecycle

Every job begins in its schema's `initial_state` and ends when a
transition lands it in a state that has no outgoing edges. The
backend enforces no cycles / no orphans / no dangling references
at save-time (see the
[FSM Designer](/docs/workflows/builder) for the compiler rules).

Spawning paths:

- **Manually** from `/admin/jobs` → **New job**.
- **Automatically** from a form submission carrying a
  `SPAWN_JOB_FROM_FORM` action. See the
  [webhook intake recipe](/docs/recipes/webhook-intake).
- **Via the API**: `POST /api/v1/tenant/jobs` with `{ tenant_id,
  schema_id, context_record_id? }`. Context record is optional —
  see below.

## Firing a transition

From the job detail view, you see every outbound transition from
the job's `current_state`. Click one to fire it. Three things happen
atomically:

1. The guard (if any) evaluates against the job's context via
   sandboxed [JSONLogic](/docs/workflows#guards-are-sandboxed).
   A `false` guard returns **409** with the context so you can
   see why.
2. `current_state` moves to the transition's `to` state.
3. A row lands in `event_log` recording `from_state`, `to_state`,
   `event_type`, actor email, and timestamp. Never updated —
   append-only per Claude.md §4.1.

Actions (`WEBHOOK`, `UPDATE_ENTITY`, `BATCH_SPAWN`, etc.) enqueue
on a separate worker. The transition itself commits even if a
downstream action fails; see
[Integrations → error semantics](/docs/integrations#error-semantics)
for the exact policy.

## Context records

Every job optionally carries a `context_record_id` pointing at an
[entity record](/docs/entities). Guards + actions evaluate
`{ "var": "field" }` against that record's `data_payload`. Two
common patterns:

- **Entity-bound schema** (`fsm_schemas.entity_name` set): the
  spawn form auto-filters context records to the declared entity
  kind; the [FSM guard composer](/docs/workflows/builder#guard-optional)
  surfaces annotations for the Variable input.
- **Unbound schema**: jobs can spawn without a context — guards
  that reference `{ "var": ... }` will see `null` and behave
  accordingly (usually false). Works fine for workflows that
  carry their state entirely in a separate system (webhook
  intake → external ticket system, etc.).

## The event log

Every job's full history is in `event_log` — append-only, one row
per transition + one row per administrative cancel. The admin
detail view renders it in reverse chronological order so the
most recent activity is on top. Each row carries:

- `from_state` / `to_state` (from is NULL on spawn)
- `event_type` — the transition name
- `actor` — email of the tenant user who fired it, or the
  platform admin if impersonated (see
  [Authentication → impersonation](/docs/auth#3-impersonation-jwt))
- `reason` — populated only for admin cancels (mandatory)

The log is your audit trail for compliance + debugging. There is
no "edit event" path; corrections happen by firing a compensating
transition.

## Admin cancel override

Real-world workflows sometimes need to terminate a job outside
the declared FSM — a shipment that was damaged in transit and
doesn't fit any "normal" terminal state. For that, FastYoke ships
an **out-of-band administrative override**:

```
POST /api/v1/jobs/:id/cancel
{ "tenant_id": "...", "target_state": "<any valid state>", "reason": "..." }
```

Key properties:

- **Bypasses guards.** The override is explicitly for cases
  where the guard model doesn't match reality. Admin JWT role
  claim required.
- **Target state must exist** in the schema — validated against
  `known_states()`. Unknown target returns 400.
- **Reason is mandatory.** Non-empty string. Lands in
  `event_log.reason` as the permanent audit record.
- **Not an FSM transition.** The event_type is the sentinel
  `__admin_cancel__`; the FSM engine never runs, no guard ever
  evaluates, no actions fire.

The cancel button is visible in the job detail view only for
admin-role users (not operators). See the platform admin's
Impersonation flow if you need to cancel on behalf of a tenant
admin who's not currently signed in.

## Filtering + search

The jobs list supports:

- **By schema** — dropdown scoped to the tenant's active schemas.
- **By current state** — once a schema is selected.
- **By actor** — any email that appears in a job's event log.
- **By date range** — created-after / updated-before.

Pagination is cursor-based; default page size is 50.

## Related

- [Workflows](/docs/workflows) — authoring the FSM shape jobs
  run against.
- [Entities](/docs/entities) — context records that drive guard
  evaluation.
- [Integrations](/docs/integrations) — outbound webhook + action
  semantics fired from transitions.
