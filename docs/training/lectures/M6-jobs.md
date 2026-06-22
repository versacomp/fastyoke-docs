---
title: Lecture M6 — Jobs
summary: Why the event log is append-only, what the admin cancel override actually bypasses, and when a self-loop transition is the right shape.
order: 7
---

# Lecture M6 — Jobs

## The mental model

A **job** is an entity row driving through an FSM schema.
Every transition firing writes a row to `event_log`. The
event log is **append-only** — no `UPDATE` is ever issued
against it. That immutability is the audit chain.

## Key concepts

- **Append-only event log.** The historical record. Pruning
  is impossible; correct authoring is bounded by the guard
  ("retry at most three times") not by post-hoc deletion.
- **Admin cancel override.** A separate endpoint (`POST
  /jobs/:id/cancel`) that writes the `current_state`
  column directly and appends one `__admin_cancel__` event
  to the log. **Bypasses the FSM entirely.** It exists
  because there's no clean way to encode "operator says
  stop, no matter what" inside an FSM.
- **Self-loop transitions.** A transition with
  `from == to`. Useful for audit-only events (driver
  check-in), retry counters, or guard-multiplexed
  fall-through. Same firing semantics as a forward edge.
- **Three rules for self-loops.** Every firing appends to
  the event log — bound it via the guard. The admin
  override never reads transitions, so you can't spin to
  defeat it. Optimistic UI looks like a no-op because the
  state column doesn't change — render `event_log`, not
  `current_state` alone.

## Common pitfalls

- **Trying to "fix history" by UPDATE-ing the event log.**
  The migrations don't let you, the application doesn't
  let you, and even if it did, the immutability is the
  whole point.
- **Using the admin override as a normal lifecycle exit.**
  Don't. Author a real terminal state with a real guard.
  The override is for genuine operator interventions.
- **Self-looping a payload counter without a guard
  bound.** You'll fill the event log with retry rows.
  Bound the self-loop by the guard or by upstream rate
  limiting.

## Where to go next

- Lab: [M6 — Jobs](/docs/training/labs/M6-jobs).
- Reference: [/docs/jobs](/docs/jobs).
