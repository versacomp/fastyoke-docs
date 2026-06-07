---
title: Compliance Yoke
summary: Continuous control monitoring and audit readiness — SOC 2 first, framework-agnostic.
order: 8
---

# Compliance Yoke

The Compliance Yoke turns audit readiness into everyday work. It ships **SOC 2
first** but is **framework-agnostic** — your controls, criteria, and evidence are
data, so the same engine carries other frameworks over time. It installs ready to
use, with a populated **SOC 2 / SCF starter crosswalk** so you can see the model
immediately and refine it to your environment.

## How it works: map your controls to criteria

Compliance frameworks define **criteria** — what an auditor tests against. You
define your own **controls** and map each to the criteria it satisfies. The
Compliance Yoke makes that mapping first-class:

- **Criteria** — the framework's units (for SOC 2, the AICPA Trust Services
  Criteria — CC1–CC9 plus Availability). These are the fixed reference.
- **Controls** — your organization's controls, each with an owner, status, and
  whether it's automated or manual.
- **Mappings** — every control-to-criterion link carries a **relationship type**
  (subset, intersects, equal, superset), so cross-framework coverage is auditable
  rather than approximate. One control can satisfy many criteria — *map once,
  cover many*.

## Finding → Corrective Action → Evidence → Verify

When a control falls short, you open a **finding** and drive it to closure with a
**corrective action**:

- **Corrective Actions** — a board moving from Identified → Assigned → In Progress
  → Evidence Submitted → Verified → Closed.
- **Evidence** — attach the proof (a config export, a policy document, an
  attestation). A corrective action can only be **verified** once its evidence is
  attached — sign-off is a privileged step.
- **Findings** — the open gaps, each tied to a control and a criterion, with a
  severity.

## Evidence connectors (read-only)

Connect your stack and the Yoke runs checks continuously, appends an immutable
result history, and opens a corrective action automatically when a control
drifts out of compliance (auto-resolving when it recovers):

- **GitHub** — organization 2FA enforcement and repo posture, via a read-only
  GitHub App installation.
- **Fly.io** — platform encryption-at-rest and infrastructure posture.
- **WorkOS** — SSO / directory MFA enforcement.
- **AWS** — a cross-account, **read-only `SecurityAudit` role** assumed via STS.
  **No static keys ever leave your account:** you create a role with the
  AWS-managed `SecurityAudit` policy whose trust policy allows our platform
  account **plus a one-time `ExternalId`** we generate for you (shown when you
  connect). We assume that role for short-lived, read-only credentials at check
  time — a leaked role ARN alone is useless. AWS checks cover encryption
  defaults (EBS/S3/RDS), S3 public-access block, CloudTrail logging, and root
  account security.

## Manual evidence & expiry reminders

Not everything is automatable. Policies, security-training records, penetration
tests and board minutes are **manual evidence**: upload and attest them with an
expiry date. When an attestation is lapsing (or lapsed), the Yoke raises the
**same finding → corrective-action path** as automated drift, and re-attesting
fresh evidence resolves it automatically.

## Registers

Four framework-as-data registers ship seeded from a real SOC 2 program and map
to your controls: **Policies**, **Personnel**, **Vendors**, and **Risks**.

## Auditor evidence room

Open an **engagement** for your audit period and invite your CPA as a
**read-only, time-boxed auditor** (a seeded Auditor role; revoke by removing the
membership at the end). Within the engagement the auditor sees, scoped to the
period:

- each control's current status, mapped criteria, and linked findings/actions;
- the **immutable, date-ranged** check-result history per control (the chain of
  custody);
- **reproducible population samples** (onboarding/offboarding/change/access
  review) — the same seed always yields the same sample.

Export **OSCAL** assessment-results (SAR) + plan-of-action-and-milestones
(POA&M) JSON, or a per-control **evidence package zip**. The product produces the
*inputs* to the examination — **your CPA authors the opinion**; we never
fabricate a test conclusion.

## Trust Center (public, opt-in)

Turn on a public posture page at a slug you choose. It shows a **readiness
summary**, your subprocessor list, and policy titles — and **nothing else**: no
raw evidence, findings, or PII. It's off until you enable it.

## Questionnaire AI (opt-in, draft-only)

With AI enabled, draft questionnaire answers grounded **only** in your control
library, with cited control ids. If there's no relevant evidence it says so
without inventing an answer, and every output is a **draft for human review** —
it never auto-publishes or signs off a corrective action.

## Getting started

Install the Compliance Yoke from the marketplace. It is a paid, recurring app —
your operator configures the subscription. On install you get the SOC 2 starter
crosswalk, sample findings, and corrective actions across every lane, ready to
adapt.
