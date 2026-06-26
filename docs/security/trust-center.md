---
title: Trust Center
summary: How FastYoke protects your data — isolation, encryption, access control, auditability, availability, and our path to SOC 2.
order: 1
---

# Trust Center

Security and privacy are foundational to FastYoke. This page summarizes the
controls that protect your data and our roadmap toward formal attestation. If
you need anything not covered here for a security review, email
**security@fastyoke.io**.

## Tenant isolation

FastYoke is multi-tenant by design, and isolation is enforced as a hard rule:
**every database query is scoped to a single tenant.** Each tenant's records
live in a dedicated per-tenant database file, and cross-tenant access is treated
as a critical defect — it is reviewed for on every change.

## Encryption

- **In transit.** All traffic is served over TLS.
- **At rest (infrastructure).** Production database volumes are encrypted at rest
  at the hosting layer.
- **At rest (field-level, optional).** The **PII / SPI encryption add-on** lets
  you tag sensitive fields so their values are encrypted before they are written,
  using **per-tenant data keys** (AES-256-GCM) wrapped under a platform key.
  Tagged values are decrypted only when read by an authorized request.

## Access control & authentication

- **Role-based access control (RBAC)** with least-privilege system roles, plus
  custom roles scoped to your tenant.
- **Two-factor authentication (TOTP / one-time codes)** and single sign-on
  (SSO) for organizations that require it.
- Rate limiting and anti-abuse protection on authentication and public surfaces.

## Auditability

Every workflow state change and every administrative override is written to an
**append-only event log** that records *what changed, by whom, and why*. These
records are immutable — they are never updated or deleted — so you (and your own
auditors) always have a complete, tamper-evident history.

## Availability, backups & recovery

- **Continuous database backups** plus **live replication** across instances.
- **Self-healing, role-aware health checks** that keep traffic on healthy nodes.
- Recovery procedures are documented and exercised, with backup-restore drills.

## Secure development

Every change ships through pull-request review with **automated checks that
block merges**:

- **Dependency vulnerability scanning** (Rust and JavaScript supply chains).
- **Committed-secret detection.**
- **Static analysis, type checks, accessibility, and end-to-end tests.**

Security exceptions are tracked with a documented reason and a short, enforced
expiry.

## Application security

- Customer-authored automation logic runs in a **sandbox with no host access**
  and strict resource limits.
- Uploaded files pass through a **multi-layer scanning pipeline**.
- Inputs are validated on both the client and the server.

## Subprocessors

FastYoke relies on a small set of vetted infrastructure and service providers.
The current list — and what each one processes — is published at
[/docs/security/subprocessors](/docs/security/subprocessors).
We update it before adding a new subprocessor and notify customers where our
agreements require it.

## Compliance status

We are **actively pursuing SOC 2** across the **Security, Availability, and
Confidentiality** Trust Services Criteria, beginning with a point-in-time
(Type I) report and progressing to Type II.

We want to be precise: **FastYoke does not yet hold a SOC 2 report.** This page
describes the controls that support that effort, several of which are inherited
from providers that already maintain their own SOC 2 / PCI attestations (our
hosting, payment, identity, and email providers). If your procurement process
needs a status update, current questionnaire responses, or a target date, contact
**security@fastyoke.io** and we'll share where we are.

## Reporting a vulnerability

We welcome good-faith security research. Please report vulnerabilities
**privately** — see our [Security Policy](https://github.com/versacomp/fastyoke2/blob/main/SECURITY.md)
or email **security@fastyoke.io**. We will not pursue or support legal action
against researchers acting in good faith.

## Privacy & data requests

For data processing agreements (DPAs), data-subject / deletion requests, or
questions about how your data is handled, contact **security@fastyoke.io**.
