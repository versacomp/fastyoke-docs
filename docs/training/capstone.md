---
title: Capstone
summary: The final integration project. Ship a working artifact that exercises ≥4 platform pillars and meets a five-category rubric. Default shape is an end-to-end Tenant Yoke; alternatives accepted by approval.
order: 3
---

# Capstone

The capstone is where the 16 modules collapse into one
working artifact. Pass / not-pass is decided against a
five-category self-graded rubric. There is no
centrally-graded submission in self-paced delivery
mode — a learner who can honestly answer "meets or
exceeds" across every category has completed the
program.

## Goal

Demonstrate end-to-end mastery by shipping a working
artifact that exercises **≥4 platform pillars** — not
just imported, actually used. Pillars include
entities, FSM authoring, jobs, forms, pages, App
Builder, Studio, extensions, Marketplace, webhooks,
notifications, messaging, bulk operations, reports,
Compliance Yoke, Yoker, Yoke Ledger, e-signatures,
billing / metering, reseller billing, and the
Strategic Partner program. Any four count.

## Default option (recommended)

**Ship a Tenant Yoke end-to-end.** Schema authoring,
FSM design, forms composition, a default posting rule
(if your Yoke touches money), a webhook subscription,
plus a small extension that surfaces the Yoke's data
in the admin shell. This is the highest-coverage path
through the platform and the default rubric maps to it
naturally.

## Alternative option

**Build a vertical industry app of your own design.**
Pick a domain (auto, healthcare, field service,
hospitality, anything else). Design 3+ entities and a
non-trivial workflow. Ship it behind a paywall in a
sandbox tenant.

## Other-approved

You can propose anything that meets the ≥4-pillar
rule. The syllabus does not prescribe a fixed list of
acceptable alternatives. Proposals get approved by
whichever mentor, instructor, or partner engineer
you're working with.

## Rubric

Score each category independently:
**falls-short**, **meets**, or **exceeds**.

- **Multi-tenancy hygiene.** Every query in your code
  scopes by `tenant_id`. Tests prove no leakage
  across tenants. The platform's prime directive is
  upheld.
- **Workflow correctness.** Every FSM transition has
  a guard or a documented reason for not having one.
  No mutable history; posted entries are immutable.
  Admin override (if used) is audit-logged.
- **Surface coverage.** ≥4 platform pillars are
  actually exercised, not just imported. A reviewer
  can point to where each pillar is used.
- **Production readiness.** Idempotency on writes that
  can retry. Error handling that fails closed. At
  least one observability hook (log, metric, or
  webhook) per pillar where applicable.
- **Documentation.** A README explaining what the
  capstone does, how to install it, and how to test
  it end-to-end in a fresh tenant. A second reader
  can stand up the capstone from your README without
  any other context.

A "meets" across all five categories means you've
completed the program. "Exceeds" in three or more
suggests you're ready to mentor someone else through
the curriculum.

## Submission

Self-paced delivery mode means there's no central
grader. Submit by:

1. Filling in the rubric honestly against your own
   artifact.
2. Linking your capstone repo (or sandbox-tenant
   walkthrough) somewhere your future self or
   employer can find it.
3. If you're working with a mentor (an iNetko
   engineer, an internal manager, an instructor),
   share the rubric and the repo for a sanity check.

## What's not in the capstone today

- **Centrally-graded submissions** — no platform-side
  grader; honor-system rubric.
- **Peer review** — capstones aren't pooled or
  cross-graded.
- **Completion certificate** — no formal
  "you finished" artifact.
- **Capstone gallery** / showcase — completed
  capstones aren't aggregated anywhere on the
  platform today.

## See also

- [Syllabus](/docs/training/syllabus) — the 16
  modules feeding into this.
- [Training overview](/docs/training) — the framing
  of the program.
