---
title: FastYoke On-Prem — air-gapped enterprise deployment
summary: FastYoke as a single binary inside your own network. No outbound internet required. Tenant databases stay on your hardware. Air-gap compatible from the ground up.
order: 7
---

::hero{eyebrow="Enterprise · Air-gap compatible" title="FastYoke inside your network — fully self-contained" tagline="A single Rust binary, a persistent volume, and your own infrastructure. Tenant databases never leave your network. No outbound traffic required. Everything the Cloud SaaS platform ships — running on your hardware."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## The one-sentence posture

FastYoke On-Prem is the same single-binary Rust monolith that runs
the cloud platform, deployed inside your own network. The audit log,
tenant databases, compiled FSM rules, and WASM scripting tier are
fully contained. Nothing calls home.

Air-gap means **literally no outbound** — not "reduced egress," not
"optional telemetry." The binary does not phone home. You can cut
the network cable after deployment.

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Who needs it

#title
1. Five regulated-sector profiles

#lede
If any of these describes your situation, the cloud option is the wrong starting point — regardless of what the cloud vendor's BAA says.

#default
<div id="profiles"></div>

::feature-grid{columns="3"}
  ::feature-card{title="Healthcare — HIPAA private cloud" icon="crm" accent="rose" summary="HIPAA BAAs with a cloud vendor don't eliminate the PHI-leaves-your-network problem. On-Prem puts PHI on your own hardware under your own BAA — no FastYoke subprocessor clause needed for patient data."}
  ::
  ::feature-card{title="Financial services" icon="accounting" accent="emerald" summary="Data-residency mandates, MAS TRM, SOC 2 audit scope. Per-tenant SQLite means each client's data is a discrete file under your control — no commingled multi-tenant database to scope."}
  ::
  ::feature-card{title="Critical infrastructure & utilities" icon="warehouse" accent="cyan" summary="OT / IT boundary requirements, ISA/IEC 62443. FastYoke runs on Linux inside the operational network. The FSM engine evaluates guards locally — no cloud API call required on state transition."}
  ::
  ::feature-card{title="Regulated international markets" icon="logistics" accent="violet" summary="GDPR data-residency, EU/UK data-localisation requirements, India DPDP, China PIPL. On-Prem pins data to a specific jurisdiction under your control — not a multi-region cloud with contractual residency guarantees."}
  ::
  ::feature-card{title="ISV embedded deployment" icon="crm" accent="amber" summary="Your product ships inside a client's private cloud or on-premise environment. FastYoke On-Prem is the right target for ISVs who build on FastYoke Runtime locally and deploy a self-contained platform inside the client's network."}
  ::
::

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
What ships

#title
2. The full platform, self-contained

#default
<div id="what-ships"></div>

FastYoke On-Prem is not a stripped-down edition. Everything the
Cloud SaaS platform ships, runs on your hardware:

- **Full FSM engine.** Every transition primitive — guards, WASM
  scripting, SLA escalation, event log, admin cancel override.
  State transitions evaluate locally with no cloud API call.
- **All marketplace apps.** CRM Suite, Yoke Ledger + GL Pack,
  Inventory, Field Service, Warehouse Management, TMS, 3PL,
  Cross-Dock, Freight Forwarding, Retail POS — the full catalog.
  Marketplace updates are operator-pulled, not pushed.
- **Forms + PDF.** Public forms, AcroForm overlay, typst-based
  PDF rendering — all compute runs on your VM. No external
  rendering service.
- **WASM scripting tier.** TypeScript on QuickJS in wasmtime,
  with fuel + memory caps. Script evaluation is fully sandboxed
  and runs in-process — no outbound call, no network dependency.
- **Yoke Ledger.** Full double-entry GL with period close, AR/AP,
  COGS posting, and year-end rollover. Financial data stays in
  your network.
- **Append-only event log.** Every FSM transition, every admin
  override, every form submission. The audit trail never leaves
  your hardware.
- **Backup pipeline.** Litestream replicates the tenant SQLite
  databases continuously to an S3-compatible target you control —
  your own MinIO instance, an on-site NAS, or a private cloud
  bucket.

- **Native mobile delivery via Substrate.** OTA bundles and build
  artifacts are served from your own hardware when Substrate is
  combined with On-Prem. PHI and operational data never leave your
  network. Requires a [Substrate](/substrate) fleet add-on.

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Deployment shapes

#title
3. Three valid topologies

#default
<div id="topologies"></div>

**Single-node (development / small teams).** One Linux VM, one
persistent volume. The FastYoke Runtime tier. SQLite WAL on the
volume, Litestream to a local backup target. Appropriate for
development environments and internal tooling teams that don't need
HA.

**Single-VM production (the standard shape).** One VM with a
persistent volume, Litestream replication to an S3-compatible
backup target. The same shape the cloud platform runs on — we
operate it for you in the cloud; you operate it yourself on-prem.
Handles production multi-tenant loads for most regulated
environments.

**Private cluster with FastYoke DB (bring your own cluster).** For
workloads that need HA, geo, or DR inside your own network, Enterprise+
customers can point FastYoke DB at their **own YugabyteDB or
CockroachDB cluster** — running on your own hardware or in your private
cloud account. FastYoke DB defaults to managed hosting on YugabyteDB
Aeon or CockroachDB Cloud; the BYOC path is the alternative for
customers with private-cloud data-sovereignty requirements where
FastYoke cannot operate the cluster on their behalf.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
4. What On-Prem costs you

#default
- **You own the ops burden.** Upgrades, backup rotation, monitoring,
  incident response, SSL certificate renewal. FastYoke publishes the
  upgrade path and runbooks; you execute them. The cloud platform
  handles this for you — On-Prem doesn't.
- **Marketplace app updates are manual.** Cloud SaaS tenants receive
  app updates automatically. On-Prem operators pull updates on their
  own schedule via the operator CLI. This is a feature for regulated
  environments (you control what version runs); it's overhead for
  everyone else.
- **No managed zero-downtime deploys by default.** The single-VM
  shape requires a brief restart for binary upgrades. Blue-green
  with the private-cluster topology is possible and documented in
  the operator runbooks; it adds infrastructure cost.
- **iNetko handles initial installs.** The binary is documented and
  the install path is straightforward for any Linux operator. In
  practice, most On-Prem engagements involve iNetko for the first
  installation, schema authoring, and FSM design — and staying on
  retainer for the harder integration work. Plan for that engagement
  cost.
- **Early access, post-GA.** On-Prem is available for early access
  alongside the platform's general release. Enterprise procurement
  conversations can start now — the install path is ready for
  qualified pilots.

::

::callout{type="info"}
**Also shipping to mobile devices?** Pair On-Prem with [FastYoke Substrate](/substrate) to serve OTA bundles and native Android + iOS builds from your own hardware. PHI and operational data stay on your network end-to-end.
::

::marketing-section{band="gray" maxWidth="3xl"}
#default

## Start the conversation

On-Prem procurement starts with a short technical conversation:
network topology, data-classification level, tenant count, and any
vertical-specific compliance posture. Email
[security@fastyoke.io](mailto:security@fastyoke.io) with the
subject *On-Prem* and we'll route you to the right conversation.

For implementation — schema authoring, FSM design, initial install,
integration wiring, and the first 60-90 days of operator training —
see the [Strategic Partner](/partners/inetko) page. iNetko handles
On-Prem deployments directly.

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request early access — On-Prem
::

::cta-button{to="/partners/inetko" variant="secondary" size="lg"}
Talk to iNetko
::

For ISVs building on FastYoke locally before embedding inside a
client network, the [FastYoke Runtime](/runtime) page covers the
development workflow.

::
