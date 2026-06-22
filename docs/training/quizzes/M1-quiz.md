---
title: Quiz M1 — Platform architecture & multi-tenancy
summary: 10 multiple-choice questions on the single-binary monolith, the fleet-of-files model, and the tenant_id prime directive.
order: 2
---

# Quiz M1 — Platform architecture & multi-tenancy

Pairs with [Lecture M1](/docs/training/lectures/M1-architecture)
and [Lab M1](/docs/training/labs/M1-architecture-trace).

**Q1.** Why does every tenant get its own SQLite database file?

a) SQLite can't handle multiple tenants in one file
b) Per-tenant files make cross-tenant queries mechanically impossible
c) It's a legacy constraint from before LiteFS
d) Each tenant's data uses a different schema

<details>
<summary>Answer + why</summary>

**b)** A cross-tenant query would have to open the wrong file
— the OS enforces the isolation, not a `WHERE` clause.

</details>

**Q2.** What does the prime directive require?

a) Every API endpoint authenticates the caller
b) Every tenant-scoped query filters by `tenant_id`
c) Every write runs in a transaction
d) Every read goes through the replica

<details>
<summary>Answer + why</summary>

**b)** Even inside a per-tenant DB, admin sessions can span
tenants, so every query enforces it as defense in depth.

</details>

**Q3.** Which is **not** a platform-scoped table exception?

a) audit
b) platform_admins
c) marketplace_entitlements
d) service_jobs

<details>
<summary>Answer + why</summary>

**d)** `service_jobs` is tenant-scoped. The platform DB
holds genuinely cross-tenant state only.

</details>

**Q4.** What does `/health/ready` report?

a) Whether the binary started
b) Whether LiteFS replication lag is within bounds
c) Whether the SPA bundle loaded
d) Whether disk space is below a threshold

<details>
<summary>Answer + why</summary>

**b)** Readiness is gated on replication lag — a replica
behind by too much can't serve consistent reads.

</details>

**Q5.** Which storage mode does the platform use for SQLite?

a) Rollback journal
b) WAL (Write-Ahead Logging)
c) Memory
d) Truncate

<details>
<summary>Answer + why</summary>

**b)** WAL with foreign keys ON is what makes SQLite
survive multi-tenant load — readers don't block writers.

</details>

**Q6.** What happens if a handler forgets to scope by `tenant_id`?

a) Returns 500 immediately
b) Linter catches it at build time
c) Silent data leak across tenants
d) Returns 401

<details>
<summary>Answer + why</summary>

**c)** Nothing automatic catches it. That's why the directive
is treated as a security-critical convention enforced by
review.

</details>

**Q7.** What's the relationship between LiteFS primary and replicas?

a) Active-active multi-master
b) Single writer, many replicas follow
c) Sharded by tenant
d) Read-replica with eventual consistency

<details>
<summary>Answer + why</summary>

**b)** Exactly one primary writes; replicas stream the
write log. The wedged-primary watchdog reboots a stuck
primary.

</details>

**Q8.** Why is the per-tenant database file shape preferred over a `tenant_id` column?

a) SQLite is faster with smaller files
b) OS-level file isolation removes a class of cross-tenant bugs
c) Migrations are easier
d) It scales horizontally

<details>
<summary>Answer + why</summary>

**b)** The threat model gains a layer the application can't
bypass even if a handler is wrong.

</details>

**Q9.** Where does tenant-scoped entity data live?

a) Platform DB
b) The tenant's own SQLite file
c) Redis
d) S3

<details>
<summary>Answer + why</summary>

**b)** Entity data is tenant-scoped by definition. Putting
it in the platform DB would defeat the fleet-of-files
model.

</details>

**Q10.** What's the right way to add tenant-shaped data to the platform DB?

a) Add a `tenant_id` column
b) Use a partitioned table
c) Don't — put it in the tenant DB
d) Use a separate platform-scoped table per tenant

<details>
<summary>Answer + why</summary>

**c)** If the data belongs to a tenant, it goes in the
tenant DB. Adding tenant-shaped tables to the platform DB
is technical debt with a security incident as the payback.

</details>
