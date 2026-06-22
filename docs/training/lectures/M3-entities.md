---
title: Lecture M3 — Entities
summary: Why entity payloads are stored schemaless, what the JSON-payload schema actually gates, and how deletes interact with FSM history.
order: 4
---

# Lecture M3 — Entities

## The mental model

A tenant authors **entities** — `vehicle`, `patient`,
`shipment` — without writing migrations. The schema is a
JSON document describing the payload; the storage is one
SQLite table per tenant with an `id`, a `created_at`, and
a `payload` JSON blob. The schema is the contract the
platform validates against at write time. The platform
never grows a column per tenant — that would explode the
database.

## Key concepts

- **JSON-payload schema.** Lists fields, types, labels,
  options, and constraints. The platform validates writes
  and the SPA renders forms straight from it.
- **Annotations.** Labels (display name), options (enum
  values), constraints (required, min/max). Annotations
  power UI generation without code.
- **Idempotency-Key header.** Optional on writes; same key
  produces the same outcome twice. Useful when the network
  is unreliable and you can't tell if your `POST`
  succeeded.
- **Retention contract on delete.** Deleting an entity row
  doesn't break referencing FSM job rows — the historical
  id is preserved so the event log stays readable. Cascade
  is opt-in per call.
- **Annotation-driven prefill.** Forms can prefill from
  payload defaults, from URL query params, or from the
  signed-in user — all wired through annotations, not
  custom code.

## Common pitfalls

- **Treating the payload as schemaless at the application
  layer too.** It isn't — the platform validates every
  write against the schema. Trying to write an
  unrecognized field returns 422, not silent acceptance.
- **Forgetting historical FSM rows on schema change.**
  Renaming a field doesn't migrate existing payloads. Plan
  for the readers to handle both shapes for a window.
- **Reaching for migrations.** There are no per-tenant
  migrations for entity payloads. If you need one, you're
  fighting the model.

## Where to go next

- Lab: [M3 — Author a vehicle entity](/docs/training/labs/M3-entities).
- Reference: [/docs/entities](/docs/entities).
