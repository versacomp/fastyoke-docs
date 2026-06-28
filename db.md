---
title: FastYoke DB — enterprise-grade distributed SQL with portability
summary: FastYoke DB (fyDB) is enterprise-grade distributed SQL hosted on YugabyteDB Aeon or CockroachDB Cloud, managed by FastYoke. Requires an Enterprise+ contract. Customers who need distributed SQL in a private cloud can bring their own cluster instead.
order: 5
---

::hero{eyebrow="Enterprise+ · Distributed SQL · Managed hosting" title="When you outgrow the single-VM, you don't outgrow FastYoke" tagline="FastYoke DB (fyDB) is enterprise-grade distributed SQL hosted on YugabyteDB Aeon or CockroachDB Cloud — managed by FastYoke on your behalf. Same code. Same schema. Same audit trail. Different substrate. Requires an Enterprise+ contract."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## The one-sentence posture

FastYoke is built on **per-tenant SQLite** for a reason — isolation
at the operating-system layer, locality at the file layer, and an
ejection clause that's a single download. **FastYoke DB** is what
we built for the workloads that need a different shape — HA,
geo, DR, multi-region writes.

FastYoke DB is a **managed service**: FastYoke provisions and
operates the distributed cluster on your behalf, hosted on
YugabyteDB Aeon or CockroachDB Cloud. Your application code,
your migrations directory, and your audit trail are unchanged.
You don't manage the cluster; you choose the engine.

You don't choose between "FastYoke" and "FastYoke DB." You choose
between substrates on a per-tenant basis, with the same application
code running across both.

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Two flavors, one managed service

#title
1. Pick the engine that fits the workload

#lede
FastYoke provisions and operates the cluster for you on the managed hosting provider you choose. The FastYoke DB layer, the SDK, the migrations directory, and the audit trail are identical across both flavors — only the engine underneath changes.

#default
<div id="flavors"></div>

::feature-grid{columns="2"}
  ::feature-card{title="YugabyteDB — hosted on Aeon" icon="warehouse" accent="amber" summary="FastYoke provisions your cluster on YugabyteDB Aeon, the managed cloud service from Yugabyte Inc. Apache 2.0 OSS engine, Postgres wire protocol, mature LISTEN / NOTIFY, SKIP LOCKED, and JSONB. The right default for most distributed FastYoke deployments — including healthcare and financial-services workloads that need a fully open-source engine posture."}
  ::cta-button{to="https://www.yugabyte.com/yugabytedb-managed/" variant="secondary" size="sm"}
  About YugabyteDB Aeon
  ::
  ::
  ::feature-card{title="CockroachDB — hosted on CockroachDB Cloud" icon="logistics" accent="cyan" summary="FastYoke provisions your cluster on CockroachDB Cloud from Cockroach Labs. Geo-distributed with consistent global writes and a strong change-feed story. BSL-licensed beyond the free tier — buyers with global-scale latency requirements who can absorb the commercial licence cost should evaluate it against Aeon."}
  ::cta-button{to="https://www.cockroachlabs.com/product/cockroachdb-cloud/" variant="secondary" size="sm"}
  About CockroachDB Cloud
  ::
  ::
::

::callout{type="note"}
**Bring your own cluster.** Customers who need distributed SQL inside
a private cloud can point FastYoke DB at their own YugabyteDB Aeon
deployment or CockroachDB cluster instead. The portability layer and
migration tooling are identical — the only difference is who operates
the cluster. This is the path for On-Prem Enterprise+ customers with
private-cloud data-sovereignty requirements. See the
[On-Prem page](/on-prem) for deployment topology details.
::

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
The portability layer

#title
2. SQLite code runs on FastYoke DB. FastYoke DB code runs on SQLite.

#default
The FastYoke DB layer is **the portability claim**. It is what lets
your application code, your migrations, your FSM guards, and your
event-log queries run unchanged across substrates:

- **SQLite → FastYoke DB.** Application code authored against the
  per-tenant SQLite contract runs against a YugabyteDB or
  CockroachDB shard without rewriting handlers. Dialect
  translation, type coercion, and behaviour-equivalent locking
  semantics ship inside the layer.
- **FastYoke DB → SQLite.** A tenant running on a distributed
  shard can be exported back to a per-tenant SQLite file for
  forensics, test scaffolding, or eject. The eject clause lives at
  the database tier, not just the codebase tier.
- **Capability probe.** Advanced engine features (`LISTEN/NOTIFY`,
  `SKIP LOCKED`, JSONB) light up automatically when running on
  Yugabyte. On SQLite the same code paths fall back gracefully.
  You don't write the engine branch yourself.
- **Same migrations directory.** A single SQL dialect serves both
  substrates after the FastYoke DB layer applies its translation
  pass. Operators run `fy db migrate` once; both fleets stay in
  sync.
- **Same audit trail.** The append-only event log is engine-
  agnostic. A tenant promoted from SQLite to a distributed shard
  carries its event-log history forward, byte-for-byte.

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
What ships when

#title
3. The customer-facing roadmap

#default
The roadmap below is the time-shaped picture of what FastYoke DB
will give customers. Each step ships in order; the dates are
customer-facing windows, not engineering schedules:

- **Available after GA release for early access** — *Platform-tier
  on FastYoke DB.* The control plane (orgs, tenants, billing, the
  marketplace) can run against either the local SQLite platform
  database or a managed Aeon or CockroachDB Cloud cluster
  provisioned by FastYoke. The capability probe lights up advanced
  Postgres features on the distributed engine and gracefully
  degrades on SQLite.
- **In design** — *Tenant-tier on FastYoke DB.* Per-tenant data
  plane gets the same dual-substrate treatment. Each tenant lives
  on per-tenant SQLite (the default) or on a managed shard (the
  Enterprise+ option), selected by tenant entitlement. The
  portability layer guarantees the same application code runs
  across both.
- **In design** — *CockroachDB Cloud flavor.* Same FastYoke DB
  layer, FastYoke provisions on CockroachDB Cloud instead of Aeon.
  Procurement decides between Yugabyte (Apache 2.0) and
  CockroachDB (BSL) based on licence posture and geo-distribution
  requirements; the application surface is unchanged.
- **Roadmap** — *Bidirectional migrate command.*
  `fy db promote --tenant <id>` walks a SQLite tenant up to a
  managed distributed shard. `fy db demote --tenant <id>` walks it
  back for export or test. The eject clause becomes a live operator
  command.

Phase numbers are intentionally absent from this list — what
matters to a buyer is the customer-visible step, not the internal
sprint that produced it. The marketing page updates as each step
moves from In design to Available.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
4. What FastYoke DB isn't for

#default
- **Per-tenant SQLite is still the right default.** For most
  workloads — SMB, single-region, no HA requirement — the
  architectural posture is unchanged. FastYoke DB exists for the
  workloads that need a different shape, not as a "next version"
  of the platform. Most tenants will never need it.
- **Enterprise+ contract required.** FastYoke DB is not available
  on Solo, Starter, Pro, or Team tiers. It requires an Enterprise+
  contract which includes dedicated onboarding, SLA commitments,
  and the cluster provisioning agreement with the hosting provider.
- **Managed hosting, not self-operated.** FastYoke manages the
  Aeon or CockroachDB Cloud cluster on your behalf. You don't get
  direct cluster admin access by default. Customers who need full
  cluster control should use the bring-your-own-cluster path.
- **Distributed SQL costs more.** A managed shard on Aeon or
  CockroachDB Cloud costs significantly more per tenant than a
  SQLite file. The line where it pays off is HA, geo, or DR with
  strict SLA requirements. Below that line, per-tenant SQLite wins
  on price *and* on operational simplicity.
- **Dialect coverage is high but not 100%.** Most application
  code runs unchanged through the FastYoke DB layer. SQLite-
  specific `PRAGMA`s, certain JSON1 functions, and a handful of
  edge-case operators need translation we haven't shipped yet.
  The training syllabus covers the workarounds.
- **YugabyteDB is open source; CockroachDB is BSL.** The Aeon
  managed service runs an Apache 2.0 engine. CockroachDB Cloud's
  underlying engine is BSL-licensed; commercial use beyond the
  free tier requires a paid Cockroach Labs licence on top of the
  FastYoke Enterprise+ contract. Procurement needs to know which
  licence shape fits *before* the engine choice is made.
- **Not a replacement for an analytics warehouse.** FastYoke DB
  is OLTP-shaped — operational transactions, real-time reads,
  per-tenant boundaries. If your workload is column-store
  aggregations across the whole tenant fleet, you need Snowflake
  / BigQuery / DuckDB-on-Parquet alongside, not in place of.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

## Next step

FastYoke DB is in early access *after* the platform's general
release and requires an Enterprise+ contract. Tell us your workload
shape now so we can route you into the right cohort:

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request early access — FastYoke DB
::

::cta-button{to="/guides/why-per-tenant-database-files" variant="secondary" size="lg"}
Read the architecture commitment
::

For Enterprise+ procurement (HA / geo / DR / bring-your-own-cluster
/ licence-posture questions), email
[security@fastyoke.io](mailto:security@fastyoke.io) and reference
*FastYoke DB* in the subject. For implementation help, see the
[Strategic Partner](/partners/inetko) page — iNetko handles the
control-plane migration shape directly.

::
