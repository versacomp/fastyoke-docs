---
title: Lab M1 — Trace a tenant-scoped query
summary: Open the FastYoke backend source. Pick one tenant-scoped GET handler and trace tenant_id from JWT extraction through SqlitePool selection to the SQL statement. End with a one-page write-up of the full path.
order: 2
---

# Lab M1 — Trace a tenant-scoped query

This lab pairs with **Module M1 — Platform architecture &
multi-tenancy** in the [syllabus](/docs/training/syllabus).

## What you'll do

Open the FastYoke backend source. Pick one tenant-scoped REST
handler (entities-list is the canonical example). Trace where
`tenant_id` enters the request, where the per-tenant
`SqlitePool` is selected, and whether the SQL statement
filters by `tenant_id` explicitly or relies on the per-tenant
DB shape alone. End with a one-paragraph or one-diagram
write-up explaining the path.

## Before you begin

- A local checkout of the FastYoke backend, or comfort
  reading source on GitHub.
- `ripgrep` (`rg`) or `grep` for code search.
- About 30 minutes of focused reading time.

## Steps

1. **Find a tenant-scoped REST handler.** Open
   `backend/src/api/entities.rs` (or use
   `rg "tenant_id" backend/src/api/`).
   **Checkpoint:** name one handler that takes a
   `CurrentUser` extractor and returns tenant data.
2. **Identify where `tenant_id` enters.** The handler takes
   `user: CurrentUser`. Trace the extractor in
   `backend/src/auth.rs`.
   **Checkpoint:** state where the `tenant_id` claim is read
   from the JWT and how the extractor refuses if it's
   missing.
3. **Identify per-tenant `SqlitePool` selection.** The
   handler calls `state.tenant_pool_manager.get_pool(...)`
   (or similar) to acquire the per-tenant database.
   **Checkpoint:** point at the line where the per-tenant
   pool is selected by `tenant_id`.
4. **Inspect the SQL.** Read the actual query the handler
   issues.
   **Checkpoint:** state whether the SQL `WHERE` clause
   includes `tenant_id = ?` explicitly, or whether the
   per-tenant DB makes that filter implicit. (Both can be
   true depending on the table — that's the learning.)
5. **Write up the trace.** One paragraph or a sketched
   diagram describing the four checkpoints above for the
   handler you chose.
   **Checkpoint:** the write-up names all four points
   (extractor, pool selection, SQL filter, schema scope).

## What you'll have at the end

A one-page write-up (text or diagram) of one tenant-scoped
query's full path from JWT to SQL result.

## Stuck?

See [Lab M1 — Solution](/docs/training/labs/M1-architecture-trace-solution).

## Next

[Lab M2 — Token walkthrough](/docs/training/labs/M2-token-walkthrough).
