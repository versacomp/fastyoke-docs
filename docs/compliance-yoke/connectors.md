---
title: Evidence connectors
summary: GitHub, Fly, WorkOS, and AWS evidence collectors — what each pulls, how to authorize them, the run-now button vs. the scheduler, and where results land.
order: 3
---

# Evidence connectors

Connectors are read-only collectors that observe a system
you're already running and write structured results back into
the Compliance Yoke. The platform ships four today.

## Authorizing a connector

Every connector is per-tenant. You authorize each one once
from the Compliance Yoke dashboard:

- **GitHub** — OAuth app at the organization scope. The
  platform reads repository settings, branch protections, and
  organization-level posture (MFA enforcement, SSO required).
- **Fly** — API token. The platform reads app posture (TLS,
  internal-port exposure, machine count, region pinning).
- **WorkOS** — admin API token. The platform reads directory
  and SSO posture for tenants that use WorkOS for sign-in.
- **AWS** — cross-account IAM role. The platform reads IAM
  posture, S3 public-access settings, and the subset of AWS
  Config rules the Yoke maps to framework controls.

A connector's status (connected / disconnected / failing) is
visible on `/connectors`.

## Run now vs. the scheduler

A background worker runs every connector on a cadence and
writes results into the shared results table.

You can also run a connector on demand. `POST /run` triggers
the next pull for one or all connectors; results land in the
same place the scheduler writes to. The run-now endpoint is
gated — unentitled callers get a 402.

`GET /results` returns the consolidated results view across
every connector. Each row is keyed by control and carries the
most recent observation, when it was taken, and which
connector produced it.

## See also: Compliance Gateway

For evidence that doesn't live in GitHub / Fly / WorkOS /
AWS — policies, vendor SOC 2 reports, internal handbooks
sitting in your cloud drive — install the **Compliance
Gateway** Marketplace listing. The Gateway is a separate
listing that reads from OneDrive and Dropbox, logs every
access, and surfaces an update diff. It complements this
Yoke; it does not replace the four connectors above.

## What's not in connectors today

- **GCP, Azure, Okta** as additional connectors.
- **Custom HTTP probes** — point the Yoke at an internal
  endpoint and have it score a control on the response.
- **Connector-side remediation** — the Yoke reads; it does not
  flip a setting in your AWS account on your behalf.

## See also

- [Frameworks & readiness](/docs/compliance-yoke/frameworks) —
  where the connector results land in the score.
- [Auditor Room](/docs/compliance-yoke/auditor-room) — how
  auditors pull a sample from the results.
- [API reference](/docs/compliance-yoke/api-reference) for
  endpoint shapes.
