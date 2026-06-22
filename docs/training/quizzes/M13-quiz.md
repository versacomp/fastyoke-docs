---
title: Quiz M13 — Compliance Yoke + Yoker
summary: 10 multiple-choice questions on deterministic sampling, the connector-vs-attestation distinction, OSCAL exports, and Yoker gating.
order: 14
---

# Quiz M13 — Compliance Yoke + Yoker

Pairs with [Lecture M13](/docs/training/lectures/M13-compliance-yoker)
and [Lab M13](/docs/training/labs/M13-compliance-yoker).

**Q1.** What does deterministic sampling guarantee about the Auditor Room?

a) Random sampling each engagement
b) Re-running at the same point-in-time produces the same sample
c) Always samples 100%
d) Samples by date only

<details>
<summary>Answer + why</summary>

**b)** Seed = `(tenant_id, control_id)`. Auditors verify
the determinism out-of-band.

</details>

**Q2.** What corpus does Yoker index?

a) Cross-tenant data
b) Tenant-scoped entity records, attachments (text/CSV/MD), and optionally docs
c) Public docs only
d) Just the entity schemas

<details>
<summary>Answer + why</summary>

**b)** Tenant-private vector index. Cross-tenant queries
hit empty context.

</details>

**Q3.** What's the gating tier for Yoker?

a) Free
b) Enterprise+ OR the paid `yoker` add-on
c) Solo
d) Pro

<details>
<summary>Answer + why</summary>

**b)** Two paths: tier or add-on.

</details>

**Q4.** Connector vs attestation?

a) Aliases
b) Connector pulls evidence automatically; attestation is a human-signed claim
c) Connector is paid; attestation is free
d) Connector is for code; attestation is for policy

<details>
<summary>Answer + why</summary>

**b)** Different sources for the same readiness sweep.

</details>

**Q5.** What does "unknown" control state mean?

a) Not yet evaluated
b) A connector reported evidence but no rule mapped it to a definitive state
c) Refused by the auditor
d) Out of scope for the framework

<details>
<summary>Answer + why</summary>

**b)** Investigate before assuming partial credit.

</details>

**Q6.** What format is the OSCAL export?

a) PDF
b) JSON + per-control evidence manifests, in a ZIP
c) Markdown
d) XML

<details>
<summary>Answer + why</summary>

**b)** OSCAL JSON is what auditors expect; evidence
manifests reference the rows.

</details>

**Q7.** How long is the OSCAL download URL valid?

a) Forever
b) Time-bound — re-fetch if it expires
c) 24h
d) Until next sweep

<details>
<summary>Answer + why</summary>

**b)** Signed + tenant-scoped + time-bound.

</details>

**Q8.** Asking Yoker about another tenant returns…

a) Cross-tenant context
b) Empty context (tenant-scoped)
c) Refusal
d) An error

<details>
<summary>Answer + why</summary>

**b)** The corpus is scoped — the query just sees no
relevant context.

</details>

**Q9.** What does the framework-readiness sweep score mean?

a) Percentage of evidence collected
b) Fraction of `met` controls over the framework's control surface
c) Time since last sweep
d) Auditor's grade

<details>
<summary>Answer + why</summary>

**b)** A control with no evidence pulls the score down.

</details>

**Q10.** Should you trust a Yoker answer without checking citations?

a) Yes — it's tenant-scoped
b) No — verify citations before quoting
c) Only if Enterprise tier
d) Only for read-only questions

<details>
<summary>Answer + why</summary>

**b)** RAG generates from the corpus; citations are your
audit trail.

</details>
