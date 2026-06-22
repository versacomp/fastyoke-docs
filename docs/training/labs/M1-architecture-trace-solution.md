---
title: Lab M1 — Solution
summary: Reference solution for Lab M1 — answers per step keyed to the lab's checkpoints.
order: 3
---

# Lab M1 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M1](/docs/training/labs/M1-architecture-trace). Work the
lab first — the friction of clicking through is intentional.
::

## Answer per step

### Step 1 — A tenant-scoped REST handler

The canonical entities-list handler lives in
`backend/src/api/entities.rs`. The function signature
(approximate) takes a `CurrentUser` extractor and returns a
list of entity records for one schema. Other tenant-scoped
handlers worth tracing: jobs (`backend/src/api/jobs.rs`),
forms (`backend/src/api/forms.rs`), files
(`backend/src/api/files.rs`).

### Step 2 — Where `tenant_id` enters

The `CurrentUser` extractor lives in
`backend/src/auth.rs`. It reads the `Authorization: Bearer
<jwt>` header, validates the JWT signature with the
platform's secret, and parses the `tenant_id` claim out of
the JWT body. If the JWT is missing, invalid, expired, or
lacks a `tenant_id` claim, the extractor returns `401
Unauthorized` and the handler never runs.

A handler that wants to refuse any non-human session also
calls `user.require_admin()` or `user.require_scope(...)` —
those refusals fire `403 Forbidden`.

### Step 3 — Per-tenant `SqlitePool` selection

The handler reaches into `AppState` (the Axum-shared
application state) and calls
`state.tenant_pool_manager.get_pool(&user.tenant_id)` (or a
similar accessor). The pool manager owns a map of
`tenant_id → SqlitePool` and lazy-opens per-tenant DBs on
first request. Every handler that touches tenant data
acquires its pool by this path.

### Step 4 — The SQL filter

For tables in the **per-tenant database** (entities,
jobs, forms, files): the per-tenant DB shape isolates rows
by construction. The SQL filter often does NOT include
`tenant_id = ?` because the DB itself is per-tenant.

For tables in the **platform database** (audit log,
marketplace entitlements, platform admins): every row
carries an explicit `tenant_id` column AND every SQL
statement filters on it. This is the documented exception
to the prime directive — see
[/docs/developers/tenant-scoping](/docs/developers/tenant-scoping).

Both patterns enforce isolation. The platform-DB pattern is
the more general one; the per-tenant-DB pattern is the
optimization that keeps tenant data physically apart.

### Step 5 — A sample write-up

> A request to `GET /api/v1/tenant/entities/customer`
> arrives at the entities-list handler. The `CurrentUser`
> extractor reads the JWT, validates it, and parses the
> `tenant_id` claim. The handler then calls
> `state.tenant_pool_manager.get_pool(&user.tenant_id)` to
> acquire the per-tenant SQLite pool. Because the `entity`
> table lives in the per-tenant DB, the SQL statement
> `SELECT * FROM entity WHERE schema_slug = ?` does not
> name `tenant_id` — but isolation is guaranteed by the
> per-tenant pool. The platform's prime directive holds
> across both patterns.

Compare to your own write-up. If your write-up named all
four checkpoints (extractor, pool selection, SQL filter,
schema scope), you've done the lab.

## Back to the lab

[Lab M1 — Trace a tenant-scoped query](/docs/training/labs/M1-architecture-trace).
