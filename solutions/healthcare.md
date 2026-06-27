---
title: Healthcare on FastYoke — patient flow, triage, EHR/ADT, HIPAA
summary: Patient throughput boards, urgent-care triage, an HL7v2 EHR/ADT connector, on-VM biometrics, and a BAA-backed HIPAA posture. Built for clinics and hospital units that want operational software without sending PHI to a third-party SaaS.
order: 1
---

::hero{eyebrow="Healthcare" title="Operational software for clinics and hospital units" tagline="Patient flow boards, urgent-care triage, an HL7v2 EHR/ADT connector, on-VM biometrics, and a BAA-backed HIPAA posture — all on a tenant database you own."}
::

::marketing-section{band="white" maxWidth="3xl"}

## The one-sentence posture

FastYoke is **operational scaffolding for healthcare** — not a certified EHR. We run the workflows around the chart (bed placement, transport, triage, throughput) on per-tenant databases with PHI encrypted at rest, and we ingest live ADT feeds so your boards stay current without a clinical-systems integration project.

::

::marketing-section{band="gray" maxWidth="6xl"}

#eyebrow
What ships today

#title
Apps + connectors for healthcare operations

#default
::feature-grid{columns="3"}
  ::feature-card{title="Patient Flow Yoke" summary="Four FSM boards out of the box — bed placement, patient transport, discharge planning, and bed turnover (EVS). Spine of every throughput conversation."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::

  ::feature-card{title="Urgent Care Triage" summary="Visit FSM + a 3-rail checkout flow. Run a same-day clinic without sending patient identity to a third-party SaaS."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::

  ::feature-card{title="EHR / ADT Connector" summary="HL7v2 admit / transfer / discharge over HTTPS keeps the patient-flow boards live without writing to the chart. Read-only, append-only, PHI-encrypted."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::

  ::feature-card{title="On-VM biometrics" summary="1:N face recognition runs inside your tenant — no SaaS face-API, no images leaving the box. Useful for staff sign-in and patient match-back."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::

  ::feature-card{title="HIPAA add-on + BAA" summary="Per-tenant AES-256-GCM field-level encryption for tagged PHI/SPI fields. BAA executed on Enterprise / ISV tier as part of HIPAA add-on enablement."}
  ::cta-button{to="/security" variant="secondary" size="sm"}
  Security posture
  ::
  ::

  ::feature-card{title="Region pinning" summary="Pin your tenant to a specific Fly.io region or region group for data-residency obligations (HIPAA-eligible US-only configs available)."}
  ::cta-button{to="/security" variant="secondary" size="sm"}
  Security posture
  ::
  ::
::
::

::marketing-section{band="white" maxWidth="3xl"}

## How healthcare data lives on FastYoke

- **Per-tenant database file.** No `WHERE tenant_id = ?` falling back as the isolation layer. A bug cannot read the wrong tenant's data because the application never opens the wrong file.
- **Per-tenant AES-256-GCM keys.** Tagged PHI / SPI fields are encrypted under per-tenant data keys wrapped by a platform key. Tagged values are opaque to filter / sort / search — they're decrypted only on authorized read.
- **Append-only event ledger.** Every state change (bed assigned, encounter discharged, ADT message received) is recorded as an immutable row. No `UPDATE` or `DELETE` is ever issued against the ledger. That's the audit trail.
- **PHI-bearing connector ledger.** The EHR/ADT connector keeps the raw HL7v2 messages in an append-only, encrypted-at-rest ledger so an audit can replay exactly what arrived from the interface engine, when, with what ACK.
- **No third-party model training.** PHI never leaves the tenant boundary for ML training or analytics; the on-VM biometric runs inside your tenant.

::

::marketing-section{band="gray" maxWidth="3xl"}

## What FastYoke is **not**

- **Not a certified EHR.** We do not bill, write to the chart, or claim CEHRT / Meaningful Use status. The connector is read-only.
- **Not a clinical decision-support tool.** Boards display state — they do not recommend care.
- **Not HIPAA-by-default.** The HIPAA add-on must be enabled, the BAA must be executed, and PHI tagging is the tenant's responsibility. Defaults are **opt-in opaque-by-default**, not silently compliant.

If you need a certified EHR or a chart-of-record system, FastYoke runs around it, not in place of it.

::

::marketing-section{band="white" maxWidth="3xl"}

## Get started

FastYoke's general release is scheduled for later this year. Healthcare workloads are evaluated cohort-by-cohort — request early access below and we'll set up a 20-minute scoping call with someone who's wired healthcare tenants before.

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request early access
::

::cta-button{to="/security" variant="secondary" size="lg"}
Read the security posture
::

For procurement or compliance review packets, email [security@fastyoke.io](mailto:security@fastyoke.io) and reference healthcare in the subject.

::
