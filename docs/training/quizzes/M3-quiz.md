---
title: Quiz M3 — Entities
summary: 10 multiple-choice questions on JSON-payload schemas, annotations, the retention contract on delete, and idempotency.
order: 4
---

# Quiz M3 — Entities

Pairs with [Lecture M3](/docs/training/lectures/M3-entities)
and [Lab M3](/docs/training/labs/M3-entities).

**Q1.** How does the platform validate entity payloads without per-tenant column schemas?

a) It doesn't — payloads are accepted as-is
b) Against the tenant's JSON-payload schema at write time
c) Through a separate validator service
d) Via foreign key constraints

<details>
<summary>Answer + why</summary>

**b)** The schema is the contract. The platform validates
every write against it and returns 422 for unknown fields.

</details>

**Q2.** Difference between a label annotation and an option annotation?

a) Labels are required, options aren't
b) Label = display name; option = enum value
c) They're aliases
d) Options apply only to forms

<details>
<summary>Answer + why</summary>

**b)** Labels rename for the UI; options enumerate the
legal values.

</details>

**Q3.** What does deleting an entity row do to historical FSM job rows that referenced it?

a) Cascades and deletes them
b) Returns 409 unless cascade is opt-in
c) Keeps the historical id so the event log stays readable
d) Marks them as orphaned

<details>
<summary>Answer + why</summary>

**c)** Retention contract — the deleted id is preserved so
event-log rows still resolve.

</details>

**Q4.** What's the `Idempotency-Key` header for?

a) Required on every write
b) Same key produces the same outcome on retry
c) Caches reads
d) Cross-tenant deduplication

<details>
<summary>Answer + why</summary>

**b)** Useful when the network is unreliable and you can't
tell if your POST succeeded.

</details>

**Q5.** What's the storage shape for entity payloads?

a) One column per field
b) One row per field
c) One JSON blob column per row
d) Object store with row metadata

<details>
<summary>Answer + why</summary>

**c)** Schemaless storage — `payload` is JSON; the platform
validates against the schema at write time but never
adds columns.

</details>

**Q6.** Can you migrate entity payloads by renaming a field in the schema?

a) Yes — the platform auto-migrates
b) No — existing payloads keep the old shape until rewritten
c) Only with admin override
d) Only on Enterprise

<details>
<summary>Answer + why</summary>

**b)** Schemaless = no auto-migration. Readers must handle
both shapes for a window.

</details>

**Q7.** What's a constraint annotation used for?

a) Foreign keys
b) Required / min / max validation
c) Cascade rules
d) Indexing hints

<details>
<summary>Answer + why</summary>

**b)** Validation rules the platform enforces at write
time.

</details>

**Q8.** What's the right way to add a new entity field that 80% of existing rows already implicitly have?

a) Add it as required with a backfill
b) Add it optional, infer/backfill in a follow-on pass
c) Block until every row is backfilled
d) Add it required with no backfill

<details>
<summary>Answer + why</summary>

**b)** Schema changes don't migrate existing rows.
Add optional, backfill in a job, then tighten if desired.

</details>

**Q9.** What does writing a field not in the schema return?

a) 200 with the field dropped
b) 422 (unprocessable entity)
c) 404
d) 500

<details>
<summary>Answer + why</summary>

**b)** The platform refuses; it doesn't silently strip.

</details>

**Q10.** Where does annotation-driven prefill pull from?

a) Only payload defaults
b) Only URL query params
c) Payload defaults, query params, or the signed-in user
d) Only the signed-in user

<details>
<summary>Answer + why</summary>

**c)** All three sources, wired through annotations rather
than per-form code.

</details>
