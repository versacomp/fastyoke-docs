---
title: Lab M13 — Solution
summary: Answer key for the M13 compliance & Yoker lab — exact sweep/evidence/OSCAL curls, Auditor Room menu paths, deterministic-sampling and gating notes.
order: 27
---

# Lab M13 — Solution

> **Reveal warning.** This page is the **answer key** for
> [Lab M13 — Compliance & Yoker](/docs/training/labs/M13-compliance-yoker).
> Try each step on your own first; the friction is where the
> learning lives.

## Step 1 — Sweep response shape

```json
{
  "framework": "soc2",
  "score": 0.78,
  "swept_at": "2026-06-22T18:00:00Z",
  "controls": [
    { "id": "CC1.1", "state": "met", "evidence_count": 4 },
    { "id": "CC1.2", "state": "gap", "evidence_count": 0 },
    { "id": "CC2.1", "state": "unknown", "evidence_count": 1 }
  ]
}
```

The score is the fraction of `met` controls over the framework's
control surface. `unknown` means the connector reported evidence
but no rule mapped it to a definitive state.

## Step 3 — OSCAL export

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"framework":"soc2"}' \
  https://www.fastyoke.io/api/v1/tenant/framework-readiness/oscal-export
```

Response:

```json
{
  "download_url": "https://exports.fastyoke.io/oscal/...",
  "expires_at": "2026-06-22T20:00:00Z",
  "size_bytes": 84210
}
```

The signed URL is tenant-scoped and time-bound — re-fetch the
endpoint if it expires.

## Step 4 — Auditor Room menu path

- **Admin → Compliance → Auditor Room → Open engagement.**
- Pick the framework + engagement window.
- Click **Download OSCAL package**.

## Three operational notes

- **Deterministic sampling.** The Auditor Room samples
  controls by `(tenant_id, control_id)` seed — re-running an
  engagement at the same point-in-time produces the same
  sample. Auditors verify the determinism out-of-band.
- **Yoker gating.** Available on Enterprise+ OR via the
  paid `yoker` add-on ($299/mo Pro+).
- **Connector vs. attestation.** A connector pulls evidence
  automatically (GitHub commits, AWS IAM policies). An
  attestation is a human-signed claim ("we ran the penetration
  test on 2026-04-12"). Both feed the same sweep.

## Back to the lab

Return to [Lab M13 — Compliance & Yoker](/docs/training/labs/M13-compliance-yoker).
