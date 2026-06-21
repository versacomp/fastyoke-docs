---
title: Frameworks & readiness
summary: Frameworks, readiness scoring, drift detection, and the controls simulator — how the Compliance Yoke turns connector results plus attestations into a per-framework score.
order: 2
---

# Frameworks & readiness

A **framework** is a named set of categories and controls — the
platform ships a curated list (SOC 2-style, HIPAA-style,
ISO-27001-style). Each tenant gets the same list; per-control
state is tenant-scoped.

A **readiness score** per framework is a roll-up of category
scores. Each category score is computed from:

- **Connector results** — what GitHub / Fly / WorkOS / AWS
  observed on the last successful run.
- **Evidence** — files and attestations sitting in the
  [vault](/docs/compliance-yoke/auditor-room#evidence-vault).
- **Attestations** — explicit operator confirmations for
  controls that aren't observable via a connector ("we run
  background checks on new hires").

The score is a number. The platform does not publish "ready /
not ready" as a binary — your auditor decides that.

## Drift detection

A control's evidence has an **expiry window**. When the
freshest evidence ages past that window, readiness falls
without re-running a check. The dashboard surfaces controls
whose evidence is about to expire so you can refresh before
they drop you below a threshold.

## The controls simulator

Before you connect a single source, the simulator lets you
model the score. Useful for pre-purchase evaluation — you can
see how close you'd be if every control had clean evidence,
and where the gap is concentrated.

| Endpoint | What it does |
| --- | --- |
| `GET /api/v1/tenant/compliance/simulation` | The current simulation state. |
| `POST /api/v1/tenant/compliance/simulation/start` | Begin a new simulation against a framework. |
| `POST /api/v1/tenant/compliance/simulation/advance` | Step forward through the simulator's decision tree. |

The simulator never writes evidence or alters real readiness.
It's a read-only "what would the score be if…" surface.

## What's not in the framework model today

- **Custom frameworks** — bring your own controls.
- **Custom category mapping** — change the weighting of how
  categories roll up.
- **Multi-framework dashboards** — a single view that shows
  every framework's score side-by-side.

## See also

- [Evidence connectors](/docs/compliance-yoke/connectors) — the
  inputs to per-category scoring.
- [Auditor Room](/docs/compliance-yoke/auditor-room) — what
  happens at audit time.
- [API reference](/docs/compliance-yoke/api-reference) for
  request and response shapes.
