---
title: Lecture M5 — FSM authoring
summary: Why guards are JSONLogic and never raw strings, what an orphan state is, and how deterministic replay works.
order: 6
---

# Lecture M5 — FSM authoring

## The mental model

An FSM schema describes a lifecycle: a set of named
**states**, named **transitions** between them, and a
**guard** on each transition that decides whether the
transition can fire. The guard is a JSON document
(JSONLogic) evaluated in a sandbox. Side-effect-free,
deterministic, and impossible to use for arbitrary code
execution.

## Key concepts

- **States and transitions.** A transition is a tuple
  `(from, to, event, guard)`. Multiple transitions can
  share an event name — the guards disambiguate.
- **JSONLogic guards.** A small, declarative language
  with arithmetic, comparison, and a few collection
  operators. No host functions. No I/O.
- **The sandbox.** Default evaluator is `jsonlogic-rs`.
  Compute-heavy logic can drop down to a `wasmtime`-hosted
  module with fuel + memory caps — same no-host-syscall
  invariant.
- **Orphan states.** A state declared in the states map
  but unreachable from `initial_state`. The validator
  catches these at save time.
- **Deterministic replay.** Given the same FSM schema, the
  same job rows, and the same `event_log`, replaying
  produces the exact same transitions. Auditors verify it.

## Common pitfalls

- **Reaching for a host function in a guard.** You'll
  immediately discover the sandbox doesn't let you. The
  fix is upstream — compute the value in the payload
  before transition.
- **Authoring a guard that depends on wall-clock time.**
  Replay won't be deterministic. Pass timestamps in as
  payload fields.
- **Authoring transitions with overlapping guards.** The
  evaluator fires the **first matching** transition — order
  in the schema matters.

## Where to go next

- Lab: [M5 — FSM authoring](/docs/training/labs/M5-fsm-authoring).
- Reference: [/docs/workflows](/docs/workflows).
