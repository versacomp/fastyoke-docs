---
title: Lab M13 — Compliance & Yoker
summary: Run a framework-readiness sweep, list collected evidence, generate an OSCAL export, open the Auditor Room, and optionally ask Yoker a tenant-scoped question.
order: 26
---

# Lab M13 — Compliance & Yoker

This lab pairs with **Module M13 — Compliance Yoke + Yoker**
in the [syllabus](/docs/training/syllabus).

## What you'll do

Run a framework-readiness sweep against your tenant via REST.
List the evidence the connectors have already collected.
Generate an OSCAL export for the auditor. Then open the
Auditor Room in the admin shell and download the export ZIP.
If you have access to Yoker, ask one tenant-scoped question
and observe the retrieval citations.

## Before you begin

- A FastYoke tenant you're an admin of.
- A `fy_pat_` PAT with `compliance:read` scope (or admin
  session JWT).
- At least one evidence connector configured (GitHub is the
  easiest — wire it up in **Admin → Compliance →
  Connectors**).
- `curl` and `jq`.
- About 45 minutes.

## Or use Postman

If you prefer a GUI, run the REST steps (Steps 1–3) from
Postman instead of `curl`. Steps 4–5 are GUI work in the
admin shell — they stay on this lab page.

**Import once:** in Postman, choose **File → Import → Link**
and paste this URL:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

Click **Import**. Open the **Week 7 — Specialized Yokes → M13 — Compliance & Yoker** folder, set the `FY_TOKEN` environment variable, then run each request in order.

The `curl` steps below remain the source of truth.

## Steps

1. **Run a framework-readiness sweep.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"framework":"soc2"}' \
     https://www.fastyoke.io/api/v1/tenant/framework-readiness/sweep
   ```

   **Checkpoint:** 200 with a posture score and a list of
   controls with their state (`met`, `gap`, `unknown`).
2. **List collected evidence.**

   ```bash
   curl -i -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/framework-readiness/evidence
   ```

   **Checkpoint:** 200 with an array of evidence items —
   each has a `connector`, a `control_id`, and a
   `collected_at` timestamp.
3. **Generate an OSCAL export.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"framework":"soc2"}' \
     https://www.fastyoke.io/api/v1/tenant/framework-readiness/oscal-export
   ```

   **Checkpoint:** 200 with a signed `download_url` and an
   `expires_at`. The URL serves a ZIP containing the OSCAL
   JSON + evidence references.
4. **Open the Auditor Room (GUI).** **Admin → Compliance →
   Auditor Room → Open engagement**. Click **Download
   OSCAL package**.

   **Checkpoint:** the ZIP downloads. Open it — there's
   one OSCAL JSON file and one per-control evidence
   manifest.
5. **(Bonus) Ask Yoker a question.** If your tenant has the
   `yoker` add-on or Enterprise+ tier, open **Admin →
   Yoker** and ask: *"Which SOC 2 controls are currently
   in `gap` state?"*

   **Checkpoint:** Yoker answers with citations pointing at
   the same control rows the sweep returned in Step 1.

## What you'll have at the end

A scored framework-readiness sweep, an evidence list, a
downloaded OSCAL ZIP, and (optionally) a Yoker answer with
citations.

## Stuck?

See [Lab M13 — Solution](/docs/training/labs/M13-compliance-yoker-solution).

## Next

[Lab M14 — Ledger & E-sign](/docs/training/labs/M14-ledger-esign).
