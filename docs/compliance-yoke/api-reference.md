---
title: API reference
summary: The REST surface for the Compliance Yoke — frameworks, readiness, connectors, simulator, evidence, engagements, OSCAL export, ZIP package, Trust Center, AI Q&A.
order: 6
---

# API reference

Every endpoint is tenant-scoped. The platform derives the
`tenant_id` from the JWT; you never pass it in the body or in
a path segment.

## Auth and gating

- **Auth:** tenant JWT in the `Authorization: Bearer …`
  header.
- **Gating:** every endpoint returns `402 Payment Required`
  unless the tenant holds either `fastyoke.compliance` (paid)
  or `fastyoke.compliance-demo` (free demo). The body of the
  402 carries a machine-readable error code and the listing
  slug:

```json
{
  "error": "compliance_not_entitled",
  "listing_id": "fastyoke.compliance"
}
```

## Frameworks & readiness

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/api/v1/tenant/compliance/frameworks` | List frameworks available on this tenant. |
| GET | `/api/v1/tenant/compliance/readiness` | Per-framework readiness, broken down by category score. |

## Connectors

| Method | Path | Purpose |
| --- | --- | --- |
| GET  | `/api/v1/tenant/compliance/connectors` | List configured connectors and their status. |
| POST | `/api/v1/tenant/compliance/connectors/github` | Connect or update GitHub. |
| POST | `/api/v1/tenant/compliance/connectors/fly` | Connect or update Fly. |
| POST | `/api/v1/tenant/compliance/connectors/workos` | Connect or update WorkOS. |
| POST | `/api/v1/tenant/compliance/connectors/aws` | Connect or update AWS. |
| POST | `/api/v1/tenant/compliance/run` | Fire an ad-hoc run; writes to the same results store the scheduler uses. |
| GET  | `/api/v1/tenant/compliance/results` | Consolidated results across every connector. |

## Controls simulator

| Method | Path | Purpose |
| --- | --- | --- |
| GET  | `/api/v1/tenant/compliance/simulation` | Current simulation state. |
| POST | `/api/v1/tenant/compliance/simulation/start` | Begin a new simulation against a framework. |
| POST | `/api/v1/tenant/compliance/simulation/advance` | Step the simulator forward. |

The simulator never writes to real readiness or evidence; it
is a read-only modeling surface.

## Evidence vault

| Method | Path | Purpose |
| --- | --- | --- |
| GET  | `/api/v1/tenant/compliance/evidence` | List evidence. |
| POST | `/api/v1/tenant/compliance/evidence` | Upload a new piece of evidence. |
| POST | `/api/v1/tenant/compliance/evidence/:id/attest` | Attest to evidence — irrevocable. |

## Audit engagements

| Method | Path | Purpose |
| --- | --- | --- |
| GET  | `/api/v1/tenant/compliance/audit/engagements` | List engagements. |
| POST | `/api/v1/tenant/compliance/audit/engagements` | Create an engagement. |
| GET  | `/api/v1/tenant/compliance/audit/engagements/:id` | Engagement detail. |
| POST | `/api/v1/tenant/compliance/audit/engagements/:id/status` | Transition status (draft → in_progress → submitted → closed). |
| GET  | `/api/v1/tenant/compliance/audit/engagements/:id/export.oscal` | NIST OSCAL JSON. |
| GET  | `/api/v1/tenant/compliance/audit/engagements/:id/package.zip` | ZIP of OSCAL + sampled evidence + cover sheet. |

## Trust Center

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/api/v1/tenant/compliance/trust-center` | Read the Trust Center config. |
| PUT | `/api/v1/tenant/compliance/trust-center` | Update the Trust Center config (slug, allowlisted controls, AI toggle). |

## AI Q&A

| Method | Path | Purpose |
| --- | --- | --- |
| POST | `/api/v1/tenant/compliance/ai/answer` | Answer a natural-language question grounded in the published posture only. |

The AI endpoint returns 404 when the per-tenant AI toggle is
off.

## See also

- [Overview](/docs/compliance-yoke) — what the surface is and
  who installs it.
- [Auditor Room](/docs/compliance-yoke/auditor-room) — what
  OSCAL and ZIP actually contain.
- [Trust Center](/docs/compliance-yoke/trust-center) — the
  allowlist model around the public surface.
