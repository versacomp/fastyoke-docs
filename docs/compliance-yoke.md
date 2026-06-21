---
title: Compliance Yoke
summary: Framework readiness, evidence collectors, an Auditor Room with deterministic sampling and OSCAL export, and a public Trust Center — wired into the platform you already run.
order: 1
---

# Compliance Yoke

The Compliance Yoke is a tenant-scoped surface that maps your
controls to industry frameworks, collects evidence from your
cloud stack on a schedule, lets external auditors pull a
deterministic sample, and exports OSCAL plus a ZIP package at
the end of an engagement. It is intended to satisfy the
evidence-collection and sampling steps of SOC 2, HIPAA, and
ISO-27001-style audits — the platform produces the artifacts;
your auditor renders the opinion.

## Who installs it

Tenants in regulated verticals (healthcare, financial services,
public-sector, ISVs with enterprise buyers). Operators who'd
otherwise stand up a separate GRC tool plus integrations plus
an evidence-bucket plus a sampler script.

## Gating

The Compliance Yoke is a paid [Marketplace](/docs/marketplace) listing. Two
entitlements satisfy the gate:

- **`fastyoke.compliance`** — the paid listing. Full surface.
- **`fastyoke.compliance-demo`** — a free demo entitlement
  scoped read-only so evaluators can see the shape before
  buying.

A request from a tenant that holds neither receives a `402
Payment Required` whose body contains the listing slug the
caller is missing. Frontend surfaces use that slug to render an
upgrade prompt.

## End-to-end tour

1. **Pick a framework.** The frameworks list reflects what the
   platform ships today; per-framework readiness is a roll-up of
   category scores computed from evidence, connector results,
   and attestations. See
   [Frameworks & readiness](/docs/compliance-yoke/frameworks).
2. **Connect a source.** GitHub, Fly, WorkOS, or AWS. Each
   connector pulls the controls it can observe (branch
   protections, MFA-on-org, IAM posture, etc.). See
   [Evidence connectors](/docs/compliance-yoke/connectors).
3. **Run a check.** The scheduler runs collectors on a cadence;
   you can also fire a run-now from the dashboard. Results land
   in a single results view alongside their controls.
4. **Publish a posture page.** The
   [Trust Center](/docs/compliance-yoke/trust-center) is an
   allowlist-published page (you choose what's visible) with an
   optional AI Q&A endpoint that answers grounded in your
   published controls.
5. **Open an engagement.** Invite an external auditor to the
   [Auditor Room](/docs/compliance-yoke/auditor-room). The
   sampler is deterministic — the same engagement window yields
   the same sample twice.
6. **Export.** At the end of the engagement, export an OSCAL
   JSON document plus a ZIP package containing the OSCAL file,
   the sampled evidence, and a cover sheet.

## What's not in the Compliance Yoke today

The following are explicit non-features. Customers who need
them should plan accordingly.

- **Custom frameworks** — bring your own controls and have the
  Yoke score them.
- **Additional connectors** beyond GitHub, Fly, WorkOS, and AWS
  (GCP, Azure, Okta, custom HTTP probes).
- **Sub-control evidence linkage** from arbitrary entities — wiring
  a sales-quote record to a control as evidence.
- **Continuous-export to GRC platforms** like Vanta or Drata.
- **Auditor-side notes and findings** UI inside the Auditor
  Room. Auditors pull samples and export today; their writeup
  lives outside the platform.

## See also

- [Frameworks & readiness](/docs/compliance-yoke/frameworks)
- [Evidence connectors](/docs/compliance-yoke/connectors)
- [Auditor Room](/docs/compliance-yoke/auditor-room)
- [Trust Center](/docs/compliance-yoke/trust-center)
- [API reference](/docs/compliance-yoke/api-reference) for the
  REST surface.
- [Permissions](/docs/permissions) — the Auditor role is created
  by the Yoke when you invite an external auditor.
- [E-sign](/docs/esign) — sealed PDFs become evidence in the
  vault.
