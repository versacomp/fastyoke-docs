---
title: FastYoke Docs
summary: The aPaaS for logistics. Multi-tenant FSMs, schema-driven entities, extensible admin surfaces.
---

# FastYoke Documentation

FastYoke is a single-binary, multi-tenant application platform that
runs operational workloads across health systems, dealerships, field
ops, sales pipelines, and back-office finance. The breadth below
shows where it lands today; the four primitives further down show
how every one of those workloads is built from the same parts.

## What you can build

FastYoke runs across these industries today — each sector below
points at a real seeded app you can install and inspect.

### Healthcare & Patient Operations

Patient throughput, bed flow, and care-coordination workloads where
the FSM tracks where a patient is in their journey.

- Bed allocation and discharge planning across units
- Triage queues that page on-call clinicians via FSM-driven actions
- Anonymous patient intake forms feeding straight into the admission
  workflow
- Compliance-grade audit log for every state change

→ See **Patient Flow Yoke** in the marketplace.

### Automotive & Service Bays

Dealer floors, service write-ups, and repair-order lifecycles where a
vehicle moves through bays, sign-offs, and parts holds.

- Auto-dealer lead-to-customer pipeline with delivery checklists
- Service bay scheduling and repair-order status boards
- Tech-side labor and parts ledger that closes the loop on dealer
  payments
- Multi-tenant per-dealership isolation out of the box

→ See **Auto Dealer Yoke** and **Auto Tech Yoke** in the marketplace.

### Sales, CRM & Field Service

Lead-to-cash pipelines and field dispatch where the FSM is the
contract between sales, ops, and the technicians on the road.

- Lead → Opportunity → Quote → Order → Customer conversion chain with
  line items and payments
- Dispatcher boards for assigning field jobs to technicians by skill
  or zone
- Job lifecycles that track on-site arrival, completion, invoicing,
  and payment
- Customer-facing forms for service requests that drop into the
  dispatch queue

→ See **CRM Suite** and **Yoke for Field Service** in the marketplace.

### Operations, Inventory & Finance

Cross-cutting workloads where the FSM enforces approval, stock
movement, or ledger posting rules across the rest of the platform.

- Inventory transfers and stock-take workflows with approval gates
- General-ledger posting rules layered on every FSM transition that
  touches money
- Compliance reviews and document-collection workflows with structured
  audit trails
- ETL connectors that pull external data into entities for downstream
  FSMs

→ See **Inventory Yoke**, **Yoke Ledger**, and **Compliance Yoke** in
the marketplace.

## The four primitives

- **Finite-state machines** drive every job through operator-authored
  transitions. Guards are sandboxed JSONLogic; actions are declarative.
- **Schemaless entities** hold your domain data as JSON payloads,
  with optional field-level [annotations](/docs/entities) for labels,
  options, and constraints.
- **Forms** collect data from anyone (authenticated or anonymous via
  public invites), validate against zod-mirrored schemas, and feed
  submissions back into your FSM.
- **Extensions** let you ship your own React UI bundled against the
  platform SDK and mounted inside the admin shell.

::section-cards
::

## Where to start

::callout{type="tip" title="Tenant admin?"}
Start with [Getting Started](/docs/getting-started) — it walks you
  from sign-in to a live public form in under ten minutes.
::

::callout{type="tip" title="Integrator?"}
Start with [Developers](/docs/developers) for the day-1 invariants
  every API consumer needs — error envelope, tenant scoping,
  idempotency, rate limits — and links into the [SDK](/docs/sdk)
  and [CLI](/docs/cli) from there.
::

::callout{type="tip" title="Setting up access?"}
See [Permissions](/docs/permissions) for the role model, the full
  permission catalog, the role-change audit log, and the frontend
  builder-gating primitive.
::

::callout{type="tip" title="Adding e-signatures?"}
See [E-signatures](/docs/esign) for the three signing modes, what
  the platform records, and how a third party verifies a sealed
  envelope offline.
::

::callout{type="tip" title="Talking to your team?"}
See [Messaging](/docs/messaging) for the in-platform DM dock,
  @mentions, and the REST + WebSocket API.
::

## Versioning

Docs follow the deployed platform — there is no multi-version sidebar.
Surfaces that are on the way out carry a visible deprecation callout
with a pointer to the replacement; see the
[Deprecation policy](/docs/deprecation-policy) for the contract.
