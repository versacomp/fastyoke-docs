---
title: Enterprise — FastYoke
summary: The enterprise application platform for regulated, operations-heavy organizations. Mechanical tenant isolation, append-only audit, deployment choice from cloud to air-gap.
order: 3
---

::hero{eyebrow="Enterprise" title="The application platform built for regulated operations" tagline="One FSM-native stack — workflow, data model, UI, audit, and integrations — with mechanical per-tenant isolation and a clean exit. Deploy managed cloud, on-prem, or mobile fleet."}
::

::marketing-section{band="white" maxWidth="3xl"}
## Platform thesis

FastYoke is an **application platform as a service (aPaaS)** purpose-built for operations-intensive organizations where process integrity, auditability, and data residency are procurement requirements—not afterthoughts.

Line-of-business capabilities—CRM, inventory, field service, patient throughput, and financial operations—are delivered as installable applications on a single platform core. Teams consolidate onto one governed environment rather than coordinating across multiple SaaS products with inconsistent data models, integration middleware, and audit trails.

State transitions are enforced through a **finite state machine** with sandboxed guard evaluation, recorded in an **append-only event log**, and exposed through **role-gated APIs**. This architecture aligns with the controls enterprise security and compliance teams expect to validate.

::enterprise-platform-diagram
::

::inline-cta-group
::cta-button{to="/downloads/fastyoke-enterprise-platform-overview.pdf" variant="primary" size="md" download}
Download board brief (PDF)
::

::cta-button{to="/developers" variant="secondary" size="md"}
See the open architecture
::
::

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Sovereignty

#title
Three guarantees incumbents cannot copy-paste

#default
::feature-grid{columns="3"}
  ::feature-card{title="Data sovereignty" summary="Each tenant operates on its own database file — isolation is mechanical, not a WHERE clause you hope never fails. Export tenant data and compiled rules on demand."}
  ::
  ::feature-card{title="Logic sovereignty" summary="Guards compile to WebAssembly with fuel and memory caps. Your business rules run in-process — no opaque cloud scripting tier."}
  ::
  ::feature-card{title="UI sovereignty" summary="Advanced App Builder output is standard Next.js/Astro you can host anywhere. You own the frontend repository."}
  ::
::
::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
Deployment

#title
Run FastYoke where your data lives

#lede
Same application code across substrates — choose managed cloud, air-gapped on-prem, native mobile fleet, or distributed SQL when scale demands it.

#default
::feature-grid{columns="4"}
  ::feature-card{title="Runtime" summary="Fully managed cloud. Works on every plan. Zero infra." accent="cyan"}
  ::maturity-badge{status="available"}
  ::
  ::cta-button{to="/runtime" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="On-Prem" summary="Your servers. Air-gap friendly. Full data sovereignty." accent="sky"}
  ::maturity-badge{status="pilot"}
  ::
  ::cta-button{to="/on-prem" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Substrate ✦" summary="Native mobile for your fleet. OTA delivery. Team+ add-on." accent="violet"}
  ::maturity-badge{status="early-access" label="Q1 2027"}
  ::
  ::cta-button{to="/substrate" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="FastYoke DB" summary="Distributed SQL for HA, geo, and DR. Enterprise+ managed hosting." accent="emerald"}
  ::maturity-badge{status="pilot" label="Enterprise+"}
  ::
  ::cta-button{to="/db" variant="secondary" size="sm"}
  Learn more
  ::
  ::
::
::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Roadmap

#title
Also in the pipeline

#default
::feature-grid{columns="1"}
  ::feature-card{title="Studio" summary="Unified visual canvas — the on-rails wizard and Advanced App Builder ship today; the single canvas view is still in development."}
  ::maturity-badge{status="development"}
  ::
  ::cta-button{to="/studio" variant="secondary" size="sm"}
  See Studio
  ::
  ::
::
::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Security & compliance

#title
Artifacts for security and compliance review

#lede
Deployment substrates, on-VM RAG posture, Compliance Yoke, and attestations — packaged for procurement packets without replacing the full Security executive summary.

#default
| Control | Posture |
| --- | --- |
| Tenant isolation | Dedicated SQLite file per tenant (or FastYoke DB substrate at scale) |
| Field-level encryption | Optional PII/SPI add-on (AES-256-GCM per tenant) |
| On-VM RAG (Yoker) | Embeddings and vector search on your VM; cited sources; Enterprise included |
| SSO | WorkOS AuthKit |
| HIPAA | BAA + add-on on Enterprise tier |
| GDPR | DPA, data-subject rights, EU region pinning |
| Accessibility | VPAT 2.5 — [download on Security](/security) |
| SOC 2 Type II | Roadmap — bridge letter on request |

::inline-cta-group
::cta-button{to="/downloads/fastyoke-enterprise-security-compliance-brief.pdf" variant="primary" size="md" download}
Download compliance brief (PDF)
::

::cta-button{to="/downloads/fastyoke-pii-security-summary.pdf" variant="secondary" size="md" download}
Download security posture (PDF)
::
::

Full posture: [Security executive summary](/security) · [Yoker RAG overview](/yoker) · [Subprocessor list](/docs/security/subprocessors) · [Trust Center (technical)](/docs/security/trust-center)
::

::marketing-section{band="white" maxWidth="3xl"}
## Identity & access

- **72-permission RBAC catalog** — every API and UI surface gates on explicit permissions.
- **Personal Access Tokens** — scoped `fy_pat_` tokens with hard refusals outside granted scopes.
- **Strategic Partner consent** — implementation partners reach a tenant only while a per-tenant grant is active.
::

::marketing-section{band="gray" maxWidth="3xl"}
## Implementation partners

Enterprise rollouts — especially **on-prem** and vertical FSM design — typically pair FastYoke with a certified implementation partner. See [Partners](/partners) and [On-Prem deployment](/on-prem).
::

::marketing-section{band="white" maxWidth="3xl"}
#title
Start the enterprise conversation

#default
::cta-button{to="/contact" variant="primary" size="lg"}
Contact sales
::

::cta-button{to="mailto:security@fastyoke.io?subject=FastYoke%20security%20questionnaire" variant="secondary" size="md"}
Security questionnaire
::

::cta-button{to="/on-prem" variant="secondary" size="md"}
On-Prem overview
::
::
