---
title: Lecture M1 — Platform architecture & multi-tenancy
summary: Why FastYoke is a single Rust binary with one SQLite database per tenant, and why every query must scope by tenant_id.
order: 2
---

# Lecture M1 — Platform architecture & multi-tenancy

## The mental model

FastYoke is one Rust binary that serves an HTTP API and a
static SPA admin shell. Each tenant gets its **own** SQLite
database file, replicated via LiteFS. The single-binary
shape keeps deploys boring; the per-tenant database shape
makes data isolation a property the operating system enforces,
not just a `WHERE` clause.

## Key concepts

- **Fleet of files.** Every tenant lives in its own
  `<tenant>.db` file under a persistent volume. A
  cross-tenant query is mechanically impossible because
  it would have to open the wrong file.
- **Platform database.** One platform-scoped SQLite stores
  the global state that genuinely doesn't belong to a
  tenant: the user registry, audit log, platform admins,
  marketplace entitlements, billing ledgers.
- **Prime directive.** Every query touching a tenant table
  scopes by `tenant_id`. The directive applies even
  *inside* a per-tenant DB — because admin sessions can
  span tenants and the wrong session reaching the wrong
  query is a security bug.
- **LiteFS replication.** A single primary writes; replicas
  follow. The `/health/ready` endpoint reports replication
  lag; the wedged-primary watchdog reboots a stuck primary.

## Common pitfalls

- **Treating SQLite like a single-user library.** It's the
  production datastore. WAL mode + foreign keys ON
  + per-tenant files is what makes it survive multi-tenant
  load.
- **Skipping the `tenant_id` clause because "the resolver
  already filtered."** Don't. The directive is defense in
  depth — every layer enforces it.
- **Treating the platform DB as a shortcut.** If the data
  belongs to a tenant, it goes in the tenant DB. Adding
  tenant-shaped tables to the platform DB is technical
  debt that gets paid back with a security incident.

## Where to go next

- Lab: [M1 — Trace a tenant-scoped query](/docs/training/labs/M1-architecture-trace).
- Reference: [/docs/developers/tenant-scoping](/docs/developers/tenant-scoping).
