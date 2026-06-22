---
title: Lecture M13 — Compliance Yoke + Yoker
summary: Why the Auditor Room uses deterministic sampling, the connector-vs-attestation distinction, and what Yoker actually indexes.
order: 14
---

# Lecture M13 — Compliance Yoke + Yoker

## The mental model

Compliance Yoke turns the platform into a continuous
readiness machine: connectors pull evidence, rules map
evidence to controls, and an **Auditor Room** packages
the result into an OSCAL export an auditor can verify.
**Yoker** is the conversational AI layer that indexes
tenant-scoped data via RAG so non-experts can ask
questions like "which controls are in gap?"

## Key concepts

- **Framework-readiness sweep.** A scored evaluation
  against a framework (SOC 2, ISO 27001). Returns
  posture score and per-control state (met, gap, unknown).
- **Connector.** Pulls evidence automatically — GitHub
  commits, AWS IAM, Okta logs. One per source.
- **Attestation.** A human-signed claim — "penetration
  test ran 2026-04-12." Connectors can't generate these.
- **Deterministic sampling.** The Auditor Room samples
  by `(tenant_id, control_id)` seed. Re-running an
  engagement at the same point in time produces the same
  sample. Auditors verify the determinism out-of-band.
- **OSCAL.** Open Security Controls Assessment Language —
  the JSON format auditors expect. The export bundles
  OSCAL JSON + per-control evidence manifests.
- **Yoker RAG.** Indexes tenant-scoped entity records,
  attachments (text/CSV/MD), and (optionally) docs. Brute-
  force cosine over a tenant-private vector index.
  Citations point at the source row.
- **Gating tiers.** Yoker = Enterprise+ OR the paid
  `yoker` add-on. Compliance Yoke = Enterprise.

## Common pitfalls

- **Trusting "unknown" controls.** Unknown means a
  connector reported evidence but no rule mapped it.
  Investigate before assuming partial credit.
- **Treating Yoker as a search engine.** It's a RAG
  layer — answers are generated from the corpus. Verify
  citations before quoting.
- **Asking Yoker about another tenant.** The corpus is
  tenant-scoped. Cross-tenant questions hit empty
  context.

## Where to go next

- Lab: [M13 — Compliance & Yoker](/docs/training/labs/M13-compliance-yoker).
- Reference: [/docs/compliance-yoke](/docs/compliance-yoke),
  [/docs/yoker](/docs/yoker).
