---
title: Hospital throughput on FastYoke — FastYoke
summary: How a clinic or hospital unit runs patient flow, transport, discharge, and EVS turnover on FastYoke — without sending PHI to a third-party SaaS.
order: 4
---

::hero{eyebrow="Vertical playbook" title="Hospital throughput on FastYoke" tagline="What a real bed-placement, patient-transport, discharge-planning, and EVS-turnover stack looks like — running on a per-tenant database with PHI encrypted at rest."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Who this is for

You operate a clinic, a hospital unit, or an
ambulatory-surgery center. You have an EHR for the chart
of record. What you don't have is good software for the
**operational layer around the chart**: which bed a
patient is waiting on, who's transporting them, whether
EVS has turned the room, who's on the discharge worklist
this hour. That layer currently lives in a whiteboard, a
shared spreadsheet, a Teams thread, or — most commonly —
all three at once.

This guide is for the operations director, charge nurse,
or unit administrator who's done a half-built internal
tool and is wondering whether there's a sensible platform
for the operational layer that *isn't* a certified EHR.

## What FastYoke is — and isn't — for healthcare

**FastYoke is operational scaffolding around the chart.**
The [Patient Flow
Yoke](/docs/marketplace) ships four FSM boards out of the
box — bed placement, patient transport, discharge
planning, and bed turnover — running on a per-tenant
SQLite database with an append-only event log. The
[EHR/ADT connector](/docs/marketplace) ingests HL7v2
admit / transfer / discharge messages from your interface
engine so the boards stay current without a clinical
integration project.

**FastYoke is not a certified EHR.** It doesn't bill,
doesn't write to the chart, and doesn't claim CEHRT or
Meaningful Use status. The connector is read-only by
construction. If you need a chart of record, FastYoke runs
*around* yours, not in place of it.

This distinction matters because it shapes the procurement
conversation. Your CMIO doesn't need to evaluate FastYoke
against Epic. Your COO does — against the spreadsheet and
the whiteboard.

## The four boards

Each is its own FSM, running against the same shared
spine of `Unit`, `Bed`, and `Encounter` entities. ADT
messages keep the spine current; the boards model the
work happening on top.

### 1. Bed placement

The flagship board. Every requested admit becomes a
`BedRequest` entity moving through:

- **Requested** → request submitted by ED or unit
- **Assigned** → bed selected, hold placed
- **Occupied** → patient in bed
- **PendingDischarge** → MD signed discharge order
- **Vacated** → patient discharged, bed pending EVS

Bed-availability constraints are guard conditions on the
*Assigned* transition. Two operators trying to assign the
same bed get a guard rejection, not a race condition.

### 2. Patient transport

`TransportRequest` entities move from **Pending** →
**Dispatched** → **InProgress** → **Completed**. Mirrors
the rideshare-style shape — a dispatcher sees the queue,
transporters claim jobs, and the audit trail records who
moved whom, when, between which rooms.

### 3. Discharge planning

`DischargePlan` entities tracked from **Initiated**
through **Coordinated** (case management aligned),
**Pending** (waiting on transport / family pickup), and
**Discharged**. The board surfaces patients who've been
medically ready for >24 hours so case management can
escalate. Length-of-stay impact gets measurable.

### 4. Bed turnover (EVS)

`CleaningTask` entities for environmental services.
**Dirty** → **InProgress** → **Clean** → ready for the
next assignment. EVS sees the queue prioritized by bed
demand; the audit trail tells you average turnover time
per unit so you can staff appropriately.

## How the data actually lives

This is the part that matters for procurement reviews:

- **Per-tenant database file.** Your tenant has its own
  SQLite file on the Fly.io volume. There is no
  `WHERE tenant_id = ?` clause that the database falls
  back on for isolation — cross-tenant access would
  require opening the wrong file, which the application
  never does.
- **Per-tenant AES-256-GCM encryption (PII add-on).**
  Tagged PHI / SPI fields encrypted under per-tenant data
  keys wrapped by a platform key. Tagged values are
  opaque to filter / sort / search — they're decrypted
  only on authorized read.
- **Append-only event ledger.** Every state change (bed
  assigned, encounter discharged, ADT message received)
  is an immutable row. No `UPDATE` or `DELETE` is ever
  issued against the ledger.
- **PHI-bearing connector ledger.** The EHR / ADT
  connector keeps raw HL7v2 messages in an append-only,
  encrypted-at-rest ledger so an audit can replay exactly
  what arrived from the interface engine, when, with what
  ACK.
- **On-VM biometrics, optionally.** Face recognition runs
  inside your tenant, not a third-party face-API.
  Useful for staff sign-in and patient match-back when
  you don't want images leaving the box.
- **HIPAA add-on + executed BAA.** Available on
  Enterprise / ISV tier as part of the HIPAA add-on
  enablement.

## What integration looks like

The realistic path for an operations director:

1. **EHR vendor conversation.** Your IT team asks the EHR
   vendor for an HL7v2 ADT feed pointed at a FastYoke
   inbound endpoint. Most interface engines (Mirth,
   Rhapsody, Cloverleaf) ship this out of the box. You're
   not asking the EHR vendor to write code — you're asking
   them to add an outbound route.
2. **Tenant provisioning.** FastYoke spins up your tenant
   with the HIPAA add-on enabled. BAA is executed before
   any PHI flows. PII tagging is configured on the entity
   schemas that hold patient data.
3. **Pilot one unit.** Run one unit (probably the busiest
   one) in parallel with the whiteboard for 30 days. Both
   systems get the data. Charge nurse compares throughput
   metrics with the new board against the whiteboard's
   tribal knowledge.
4. **Expand.** When the unit trusts the board more than
   the whiteboard, expand to the rest of the hospital.
   The transport, discharge, and EVS boards come online
   in sequence, not all at once.

This is **weeks, not quarters.** No clinical-systems
integration project. The HL7v2 ADT feed is the only
integration point; everything else is operator-driven
inside the FastYoke tenant.

## Honest tradeoffs

FastYoke is not a hospital information system:

- **Not a certified EHR.** You still need one. FastYoke
  runs alongside it.
- **No clinical decision support.** Boards display state;
  they don't recommend care.
- **HIPAA-by-default, not by accident.** The HIPAA add-on
  must be enabled and the BAA executed; PHI tagging is
  the tenant's responsibility. Defaults are *opt-in
  opaque-by-default*, not silently compliant.
- **The ADT connector is read-only.** Charges, orders,
  and chart edits all stay in the EHR. By design — but if
  you want a system that writes to the chart, FastYoke is
  the wrong shape.

If any of those is a deal-breaker, you need a different
class of product, not a different vendor.

## How to start

1. Email `security@fastyoke.io` with "healthcare" in the
   subject. Reference this guide. You'll get the
   procurement packet (security posture, BAA template,
   SOC 2 roadmap, encryption attestation).
2. Request [early access](/getting-started) — healthcare
   workloads are evaluated cohort-by-cohort, and we'll
   schedule a 20-minute scoping call with someone who's
   wired healthcare tenants before.
3. Read the [/solutions/healthcare](/solutions/healthcare)
   page if you haven't — it covers the apps and the
   security posture in one place.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

For procurement or compliance review packets, email
[security@fastyoke.io](mailto:security@fastyoke.io) and
reference *healthcare* in the subject line.

::
