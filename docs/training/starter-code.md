---
title: Starter code
summary: Skeleton scripts and JSON templates the labs reference — clone, set two env vars, run.
order: 7
---

# Starter code

The labs in Weeks 1–8 are deliberately bare — no
hand-holding scaffolds — but a small set of working
scripts and JSON templates lives at
[`training-starter/`](https://github.com/versacomp/fastyoke2/tree/main/training-starter)
in the platform repo. They're a parachute, not a
template: clone when a step needs more than you can
type fresh.

## What's in it

| Module | Path                          | What it does                                                           |
| :----: | ----------------------------- | ---------------------------------------------------------------------- |
|   M2   | `M2-token-walkthrough/`       | `/auth/me` + mint a scoped PAT + prove the scope hard-refusal.         |
|   M3   | `M3-vehicle-crud/`            | Seed N `vehicle` records, list, delete.                                |
|   M5   | `M5-fsm-schemas/`             | Reference FSM schemas — `service_ticket`, `inventory_pick`.            |
|  M11   | `M11-webhook-receiver/`       | Node HMAC-verifying receiver with delivery-id dedup + forced-fail mode.|
|  M12   | `M12-bulk-ops/`               | Preview-then-commit bulk-edit driver.                                  |
|  M14   | `M14-esign-verify/`           | Verify a sealed PDF via the public verify endpoint.                    |
|  M15   | `M15-wallet-drain/`           | Credit the sandbox wallet, drive transitions until low-balance trips.  |

## Two env vars

Every script reads `FY_PAT` (a `fy_pat_` PAT in the
tenant you're learning in) and `FY_BASE_URL` (your
platform URL — production or sandbox).

```bash
cp .env.example .env
$EDITOR .env
set -a; source .env; set +a
```

The M11 receiver also reads `FY_SECRET` (the
`signing_secret` shown once at subscription time).

## How to use it during a lab

The lab steps are the source of truth. If a step says
"author the schema," author it yourself first. If
authoring blocks you for 10+ minutes, copy from the
starter, finish the lab, then re-author cold afterward.
Predict-then-verify beats clone-and-shrug.

## Why not its own repo?

We may split `training-starter/` into its own repository
later. For now it lives in the platform monorepo so the
two stay in sync: when the API surface changes, the
starter changes in the same PR.

## See also

- [Syllabus](/docs/training/syllabus) — the canonical
  module list.
- [Labs](/docs/training/labs) — the hands-on exercises
  these starters back.
- [Lectures](/docs/training/lectures) — the narrative
  companions.
- [Quizzes](/docs/training/quizzes) — the self-check
  banks.
