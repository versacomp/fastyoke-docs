# Workflows

> Finite-state machines with sandboxed guards, declarative actions, and multi-version publishing.

# Workflows

A workflow is a finite-state machine. You draw it as a graph;
FastYoke stores it as a strict JSON contract and runs it against
**jobs** — live FSM instances bound to the schema they were spawned
from.

## Mental model

- **Schema** — the graph itself. Immutable once saved as a version.
  Lives in `fsm_schemas` (append-only).
- **Job** — a single running instance of a schema. Carries a
  `current_state` and an optional `context_record_id` pointing at
  an `entity_records` row.
- **Transition** — a named edge with an `event_type`, an optional
  JSONLogic `guard`, and an ordered list of side-effect
  `actions`.
- **Event log** — every transition writes an append-only
  `event_log` row (also immutable). Your audit trail is always
  complete.

## Guards are sandboxed

Guard conditions never touch raw string parsing. Every guard runs
through [`jsonlogic-rs`](https://github.com/bestowinc/jsonlogic-rs)
on the server — the same evaluator the admin composer uses in the
browser for preview. That means:

- No eval. No Function(). No way for an admin to author a
  transition that reads arbitrary memory.
- Deterministic: the same `(guard, context)` pair always resolves
  the same way.
- Typed variable refs: `{ "var": "answer_kind" }` resolves against
  the job's current data; unknown variables yield `null`.

## Cancel is an out-of-band override

FastYoke deliberately does NOT wire "cancelled" into the FSM graph.
Admin cancellation is a separate `POST /jobs/:id/cancel` endpoint
that bypasses guard evaluation, writes `current_state` directly,
and appends a `__admin_cancel__` event log row with the operator
email + mandatory reason. The audit trail captures both the
override and its justification.

## Inheritance

Enterprise tenants participating in org-scoped governance receive
read-only mirrors of master templates. The builder renders these
as locked — edits happen upstream in the master tenant and
propagate via the inheritance sync engine.
