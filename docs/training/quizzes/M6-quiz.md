---
title: Quiz M6 — Jobs
summary: 10 multiple-choice questions on the append-only event log, the admin cancel override, self-loops, and optimistic UI.
order: 7
---

# Quiz M6 — Jobs

Pairs with [Lecture M6](/docs/training/lectures/M6-jobs)
and [Lab M6](/docs/training/labs/M6-jobs).

**Q1.** What does the admin cancel override bypass?

a) Just the guard on the current transition
b) The entire FSM transition engine
c) The event log
d) Tenant scoping

<details>
<summary>Answer + why</summary>

**b)** It writes `current_state` directly and appends one
`__admin_cancel__` event. Guards aren't consulted.

</details>

**Q2.** Why is the event log append-only?

a) Performance
b) Audit chain integrity
c) SQLite limitation
d) Backwards compat

<details>
<summary>Answer + why</summary>

**b)** Immutability *is* the audit value. UPDATE would
defeat it.

</details>

**Q3.** When is a self-loop the right shape?

a) Always — they're equivalent to forward edges
b) For audit-only events, retries, or counter increments
c) When you can't author a forward edge
d) Never — they're a code smell

<details>
<summary>Answer + why</summary>

**b)** Specific use cases where state doesn't change but
the event matters.

</details>

**Q4.** What is **not** a valid use of self-loops?

a) Driver check-in audit event
b) Retry counter increment
c) Spinning to defeat the admin override
d) Guard-multiplexed event fall-through

<details>
<summary>Answer + why</summary>

**c)** The override never reads transitions — it bypasses
the engine. Self-loops can't outwit it.

</details>

**Q5.** What does optimistic UI show when a self-loop fires?

a) The new state
b) Nothing — the state column doesn't change
c) The previous state
d) An error

<details>
<summary>Answer + why</summary>

**b)** Looks like a no-op. Surfaces that should reflect
self-loops render `event_log`, not `current_state` alone.

</details>

**Q6.** How do you bound a self-loop that auto-fires?

a) Set `max_fires` in the schema
b) The platform caps it at 100
c) Via the guard itself or upstream rate limiting
d) Can't be bounded

<details>
<summary>Answer + why</summary>

**c)** No automatic cap. Author a guard like `retry_count
< 3`.

</details>

**Q7.** What's the right way to record "operator says stop, no matter what"?

a) Author a `Cancelled` state in the FSM
b) Use the admin cancel override
c) Patch `current_state` directly
d) Delete the job

<details>
<summary>Answer + why</summary>

**b)** The override exists because cancellation doesn't
cleanly fit into an FSM's guard model.

</details>

**Q8.** When the admin override fires, what does it leave behind?

a) Nothing
b) A row in event_log with `event_type:
   "__admin_cancel__"` plus actor and reason
c) An UPDATE on event_log
d) A separate audit_log table row

<details>
<summary>Answer + why</summary>

**b)** Append-only — the override's audit trail rides on
the same event-log spine.

</details>

**Q9.** Can the override fire to any state in the schema?

a) Yes — any state listed in the schema's known states
b) Only terminal states
c) Only `Cancelled`
d) Only forward states

<details>
<summary>Answer + why</summary>

**a)** Target validated against the schema's known states;
unknown states return 400 before touching the DB.

</details>

**Q10.** What's the right way to "fix history" if a job transitioned through a wrong state?

a) UPDATE the event log
b) DELETE the wrong row
c) Author a forward transition that captures the correction
d) Use the admin override to revert state

<details>
<summary>Answer + why</summary>

**c)** History is immutable. The fix is a new event that
*records* the correction.

</details>
