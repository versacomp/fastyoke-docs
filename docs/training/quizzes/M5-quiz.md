---
title: Quiz M5 — FSM authoring
summary: 10 multiple-choice questions on JSONLogic guards, the sandbox, orphan states, and deterministic replay.
order: 6
---

# Quiz M5 — FSM authoring

Pairs with [Lecture M5](/docs/training/lectures/M5-fsm)
and [Lab M5](/docs/training/labs/M5-fsm-authoring).

**Q1.** Why is JSONLogic the guard language?

a) It's the fastest evaluator
b) Declarative + sandbox-safe + no host syscalls
c) Frontend uses it
d) JSON is easier to author

<details>
<summary>Answer + why</summary>

**b)** Guards must be safe to run on untrusted tenant
input. JSONLogic has no I/O.

</details>

**Q2.** What's an orphan state?

a) A state with no transitions out
b) A state declared but unreachable from `initial_state`
c) A state with conflicting guards
d) A self-loop

<details>
<summary>Answer + why</summary>

**b)** Unreachable → orphan. The validator catches it at
save time.

</details>

**Q3.** What does the guard sandbox refuse?

a) Math
b) Arbitrary code execution + I/O + host syscalls
c) Strings
d) Booleans

<details>
<summary>Answer + why</summary>

**b)** Math/strings/booleans are fine. I/O and arbitrary
code are not.

</details>

**Q4.** A transition with `from == to` is…

a) Invalid — the validator rejects it
b) A self-loop — legitimate for audit events or counters
c) Only allowed in admin override
d) An idempotency marker

<details>
<summary>Answer + why</summary>

**b)** Self-loops are first-class — same firing semantics
as forward edges.

</details>

**Q5.** Multiple transitions share an event name. Which fires?

a) All of them, in declaration order
b) The first matching guard wins
c) The platform refuses to compile the schema
d) Random selection

<details>
<summary>Answer + why</summary>

**b)** Order matters. Disambiguating by guard is the
intended pattern.

</details>

**Q6.** Why is wall-clock time inside a guard a problem?

a) It's slow
b) It breaks deterministic replay
c) Not implemented
d) Returns null

<details>
<summary>Answer + why</summary>

**b)** Replay must reproduce the same transitions. Pass
timestamps in via payload.

</details>

**Q7.** When does the compute-heavy `wasmtime` evaluator kick in?

a) Automatically when JSONLogic is slow
b) Only when the schema explicitly declares a WASM guard module
c) On Enterprise tenants
d) Never — JSONLogic only

<details>
<summary>Answer + why</summary>

**b)** Opt-in per guard. Fuel + memory caps preserve the
no-syscall invariant.

</details>

**Q8.** What happens at save if you author a schema with an orphan state?

a) The validator rejects it
b) The platform warns but accepts
c) The state is silently dropped
d) Nothing — runtime catches it later

<details>
<summary>Answer + why</summary>

**a)** Save-time validation. Catches the bug before any
job is created.

</details>

**Q9.** How does the FSM relate to the immutable event log?

a) The event log records the schema, not transitions
b) Each transition firing appends one event_log row
c) The log buffers transitions for later commit
d) Only failed transitions log

<details>
<summary>Answer + why</summary>

**b)** Every firing appends — that's the audit chain.

</details>

**Q10.** What can a guard *not* read?

a) The job's entity payload
b) The triggering event's payload
c) Arbitrary tenant rows
d) Inline constants

<details>
<summary>Answer + why</summary>

**c)** Guards see only the job + event context they're
given. Cross-row lookups happen upstream and pass values
in.

</details>
