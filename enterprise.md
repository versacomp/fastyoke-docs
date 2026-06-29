---
title: Enterprise — FastYoke
summary: The enterprise application platform for regulated, operations-heavy organizations. Mechanical tenant isolation, append-only audit, deployment choice from cloud to air-gap.
order: 3
---

::hero{eyebrow="Enterprise" title="The application platform built for regulated operations" tagline="One FSM-native stack — workflow, data model, UI, audit, and integrations — with mechanical per-tenant isolation and a clean exit. Deploy managed cloud, on-prem, or mobile fleet."}
::

::marketing-section{band="white" maxWidth="3xl"}
## Platform thesis

FastYoke is an **application platform as a service (aPaaS)** for organizations that run on workflows, not spreadsheets. CRM, inventory, field service, healthcare throughput, and accounting are apps on a shared engine — not five SaaS subscriptions held together with exports and prayer.

Every state change flows through a **finite state machine** with sandboxed guards, an **append-only event log**, and **role-gated APIs**. That is the substrate enterprise auditors expect.

::cta-button{to="/developers" variant="secondary" size="md"}
See the open architecture
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
<div class="mt-4 grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 text-center">
  <div class="rounded-xl border p-5 flex flex-col items-center gap-2" style="border-color: var(--brand-border-subtle); background: var(--brand-bg-surface)">
    <div class="flex items-center justify-center shrink-0" style="width: 36px; height: 36px; border-radius: 8px; background: rgba(56, 189, 248, 0.14); color: #38bdf8" aria-hidden="true"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><path d="M7 18h10"/><path d="M12 15v3"/><path d="M18 10.5a3.5 3.5 0 0 0-6.4-2.1 4.5 4.5 0 0 0-8.6 2.1A3.5 3.5 0 0 0 7 14.5h11"/></svg></div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">Runtime</h3>
    <p class="text-sm text-[var(--brand-text-secondary)]">Fully managed cloud. Works on every plan. Zero infra.</p>
    <a href="/runtime" class="text-sm font-medium text-[var(--brand-primary)]">Learn more →</a>
  </div>
  <div class="rounded-xl border p-5 flex flex-col items-center gap-2" style="border-color: var(--brand-border-subtle); background: var(--brand-bg-surface)">
    <div class="flex items-center justify-center shrink-0" style="width: 36px; height: 36px; border-radius: 8px; background: rgba(148, 163, 184, 0.14); color: #94a3b8" aria-hidden="true"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><rect x="5" y="5" width="14" height="6" rx="1"/><circle cx="8" cy="8" r="1"/><rect x="5" y="13" width="14" height="6" rx="1"/><circle cx="8" cy="16" r="1"/></svg></div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">On-Prem</h3>
    <p class="text-sm text-[var(--brand-text-secondary)]">Your servers. Air-gap friendly. Full data sovereignty.</p>
    <a href="/on-prem" class="text-sm font-medium text-[var(--brand-primary)]">Learn more →</a>
  </div>
  <div class="rounded-xl border p-5 flex flex-col items-center gap-2" style="border-color: var(--brand-border-subtle); background: var(--brand-bg-surface)">
    <div class="flex items-center justify-center shrink-0" style="width: 36px; height: 36px; border-radius: 8px; background: rgba(129, 140, 248, 0.14); color: #818cf8" aria-hidden="true"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><rect x="8" y="3" width="8" height="18" rx="2"/><path d="M11 18h2"/><path d="M16 7a2 2 0 0 1 0 3"/></svg></div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">Substrate <span style="color: var(--brand-primary)">✦</span></h3>
    <p class="text-sm text-[var(--brand-text-secondary)]">Native mobile for your fleet. OTA delivery. Team+ add-on.</p>
    <a href="/substrate" class="text-sm font-medium text-[var(--brand-primary)]">Learn more →</a>
  </div>
  <div class="rounded-xl border p-5 flex flex-col items-center gap-2" style="border-color: var(--brand-border-subtle); background: var(--brand-bg-surface)">
    <div class="flex items-center justify-center shrink-0" style="width: 36px; height: 36px; border-radius: 8px; background: rgba(52, 211, 153, 0.14); color: #34d399" aria-hidden="true"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><ellipse cx="12" cy="6" rx="8" ry="3"/><path d="M4 6v6c0 1.7 3.6 3 8 3s8-1.3 8-3V6"/><path d="M4 12v6c0 1.7 3.6 3 8 3s8-1.3 8-3v-6"/></svg></div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">FastYoke DB</h3>
    <p class="text-sm text-[var(--brand-text-secondary)]">Distributed SQL for HA, geo, and DR. Enterprise+ managed hosting.</p>
    <a href="/db" class="text-sm font-medium text-[var(--brand-primary)]">Learn more →</a>
  </div>
</div>
::

::marketing-section{band="gray" maxWidth="3xl"}
## Compliance & trust

| Control | Posture |
| --- | --- |
| Tenant isolation | Dedicated SQLite file per tenant (or FastYoke DB substrate at scale) |
| Field-level encryption | Optional PII/SPI add-on (AES-256-GCM per tenant) |
| SSO | WorkOS AuthKit |
| HIPAA | BAA + add-on on Enterprise tier |
| GDPR | DPA, data-subject rights, EU region pinning |
| Accessibility | VPAT 2.5 — [download on Security](/security) |
| SOC 2 Type II | Roadmap — bridge letter on request |

Full posture: [Security executive summary](/security) · [Subprocessor list](/docs/security/subprocessors) · [Trust Center (technical)](/docs/security/trust-center)
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
