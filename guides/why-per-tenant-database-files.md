---
title: Why per-tenant database files — FastYoke
summary: The "Multi-Tenancy Prime Directive," explained for buyers. Why FastYoke isolates customer data at the operating-system layer instead of trusting WHERE tenant_id = ?.
order: 5
---

::hero{eyebrow="Architecture" title="Why per-tenant database files" tagline="Every multi-tenant SaaS will tell you tenant data is isolated. The interesting question is what's holding the isolation up. Here's the architectural choice FastYoke made differently, and what it buys you."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## The default that most multi-tenant SaaS ships

Pick any multi-tenant SaaS product built since 2010 and
the data model is almost always the same: **one shared
database, one or more tables per resource, every row
tagged with a `tenant_id` column.** Every query has a
`WHERE tenant_id = ?` clause. Every endpoint resolves the
caller's tenant from the session and threads that value
through.

The application code is solely responsible for never
forgetting that clause. If a developer ships a query
without it — or with the wrong tenant id — *every
tenant's data is exposed by that one query*. The
database can't help you, because the rows look the same
to it.

This is the world most SaaS lives in. It works
*mostly*. It produces a steady tail of CVEs, postmortems,
and incident reports that begin "a query was missing its
tenant scope." The pattern is so common it has a name:
**broken object-level authorization** — number one on the
OWASP API top-10 for several years running.

## What FastYoke does instead

**Every tenant gets its own SQLite database file.**

The application doesn't have one database connection that
multiplexes between tenants. It opens *the file that
belongs to that tenant*, runs the query, and closes the
handle. There is no `tenant_id` column on the tenant-side
tables — there's no need, because no other tenant's rows
live in the file in the first place.

Cross-tenant access isn't *guarded against*. It's
**mechanically impossible.** A bug that forgets to scope
a query can't leak anyone else's data, because the
application never has two tenants' files open at the same
time. The wrong query against the wrong file just
returns the wrong tenant's own rows. Hostile inputs that
would coax a `tenant_id` parameter astray have nothing to
coax — the parameter isn't in the query.

This is what the project's [CLAUDE.md](https://github.com/versacomp/fastyoke2)
calls the "Multi-Tenancy Prime Directive," and it shapes
the rest of the architecture.

## What that buys you

### Architectural authorization

Code review for tenant isolation becomes a different kind
of question. Instead of "did the developer remember the
`WHERE` clause," reviewers ask "which file did this code
path open?" That question has a single answer per request
— `connection_for(tenant_id)` — and it's invariant across
every handler. The proof reduces to one helper, not
dozens of queries.

### A real exit story

Per-tenant files mean **your data is one file**. If you
want to leave FastYoke, you don't run a query that fans
out across a shared schema. You download your file. The
SQLite format is open, documented, and supported by every
language. The marketing claim "you can take your data
with you" becomes a single `curl` invocation, not a
six-month migration project.

For ISVs building on top of FastYoke this matters even
more: your customer's data lives in *their* file, and
they can demand a copy any time without us being involved.

### Backup and forensics simplicity

A backup is the file. A point-in-time copy is the file at
a moment. A forensic investigation is the file plus the
event log inside it. The story is the same shape no
matter which tenant is asking.

### Compliance reviews

Buyers in regulated workloads (healthcare, financial
services, government) almost always ask "where exactly
does my data live, and what stops the developer who
shipped a bug yesterday from leaking it tomorrow?" The
answer that satisfies them is rarely "we have a code
review process." The answer that does satisfy them is
"the application never has another tenant's file open at
the same time as yours."

Per-tenant files turn this from a process answer into an
architectural answer.

### Region pinning, free

Need a tenant to live in `us-east-1` for a residency
requirement? Move the file to `us-east-1`. The boundary
of *what is the tenant's data* is the file. No shared
table to split.

## What that costs

This is real engineering, not a free win.

### You can't run a cross-tenant aggregate query

Want "total submissions across all tenants today"? In a
shared-schema model that's one query. In FastYoke, it's a
fan-out across every tenant file plus a roll-up. We
maintain a separate **platform** database for the
aggregate queries that have to span tenants — billing
counters, marketplace metadata, super-admin views. The
rule we hold is that *no tenant data* lives in the
platform database. Aggregate counts do.

### File-handle and per-tenant resource pressure

Thousands of tenants means thousands of files. SQLite is
lightweight enough that this works, but the application
has to be careful about connection pooling, file
descriptor limits, and per-tenant write contention. We
size for this; you don't have to think about it.

### Cross-tenant joins simply aren't available

Some product shapes — "show me all customers across all
my white-labeled brands" — don't fit. The platform's
honest answer is "FastYoke is the wrong substrate for
that." If your product *is* multi-account-as-one-view,
shared-schema is the right pattern. We pick the workloads
where the isolation guarantee is worth more than the
flexibility lost.

### Litestream / LiteFS operational shape

Replicating one file per tenant has a different
operational story than replicating one shared database.
We've built around it (Litestream + LiteFS, with a
[wedged-primary watchdog](https://github.com/versacomp/fastyoke2/pull/781)
for the failure mode where the primary stops accepting
writes). It works, but it's a more bespoke story than
"managed Postgres with a read replica."

## Where this fits in the bigger picture

Per-tenant files are *one* piece of FastYoke's isolation
posture, not the whole thing:

- **OS-layer isolation** — per-tenant files (this guide).
- **Application-layer enforcement** — RBAC catalog, PAT
  scoping with hard refusals, Strategic Partner consent.
- **Encryption boundary** — per-tenant AES-256-GCM keys
  for tagged PII / SPI fields.
- **Audit boundary** — append-only event ledger per
  tenant.
- **Network boundary** — TLS in transit, region pinning
  for residency.

Per-tenant files are the load-bearing one. Everything
else is a layer of belt-and-suspenders on top of an
architecture that already makes the bad outcome
mechanically impossible.

## The honest summary

FastYoke chose per-tenant files because we believe most
multi-tenant SaaS leaks are not *failures of process* —
they're *consequences of the substrate*. A substrate
where the right query and the wrong query look identical
to the database will produce wrong queries no matter how
good your code review is. We removed the substrate.

This costs us cross-tenant flexibility, which is real.
For the workloads we're built for — operations-heavy
SMB, ISVs shipping to enterprise, regulated workflows
where the buyer cares more about boundary than about
query convenience — that's the right trade.

If you're evaluating FastYoke for one of those shapes,
this is the architectural commitment underneath every
other claim on `/security`. The rest of the security
posture is real, but it's defense-in-depth around a
boundary that's *already* enforced by the operating
system.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

For the procurement-grade detail — encryption,
attestations, subprocessor list — see
[/security](/security). For an architectural conversation
with someone who'd be the engineer behind your tenant,
request [early access](/getting-started).

::
