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

## Self-loops are first-class

A transition is allowed to return to the same state it started from
— a **self-loop**. The engine treats it like any other transition:
the guard evaluates, the `current_state` column gets a same-value
write, an `event_log` row appends, and live clients see the
broadcast. The audit row *is* the artifact.

Reach for a self-loop when you want to record that something
happened without advancing the job:

- **Audit-only events** — a driver checks in, a customer calls, an
  operator adds a note. The job's lifecycle position is unchanged
  but the timeline gains a row.
- **Idempotent retries** — resend a webhook or re-run an algorithm
  while a downstream guard hasn't yet cleared. The job stays
  `pending_delivery` (or whatever) until the forward edge becomes
  fireable.
- **Counters and accumulators** — bump a `retry_count` field, append
  to an `attempt_history` array, stamp a `last_seen_at` timestamp.
  The payload changes; the state doesn't.
- **Either-or events** — same event name on two transitions: one
  forward edge for the ready path, one self-loop for the not-ready
  path. The first guard that resolves true wins.

> **event_log is append-only**
>
> Every self-loop firing writes a row that you cannot prune later.
>   When you wire an automated scheduler to fire a self-loop, bound
>   it — either in the guard itself (e.g. <code>retry_count &lt; 3</code>)
>   or via your scheduler's own rate-limit logic. Unbounded loops
>   pile up audit rows fast.

> **The cancel override still wins**
>
> A self-loop cannot prevent admin cancellation. The
>   <code>POST /jobs/:id/cancel</code> endpoint writes
>   <code>current_state</code> directly without consulting the FSM
>   graph, so a job spinning in a self-loop is just as cancelable as
>   any other job.

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
