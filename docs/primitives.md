---
title: Engine Primitives
description: Composable engine logic for tenant-authored Yokes — atomic field mutations, notifications, scheduled tasks, approval workflows, and more.
---

# Engine Primitives

Engine Primitives are pre-built, security-vetted building blocks you attach to your Yoke's transitions, states, and fields. Each one is a small, composable unit of "engine" logic that you configure via the Yoke Studio without writing any code.

Pick a primitive below to learn how to use it:

| Primitive | What it does | Tier |
|---|---|---|
| [Aggregator](/docs/primitives/aggregator) | Roll up child records into a parent field — sum, count, average. | Solo+ |
| [Notifier](/docs/primitives/notifier) | Send email or webhook on transitions, state changes, or field updates. | Solo (email) / Pro (webhook) |
| [Scheduler](/docs/primitives/scheduler) | Run transitions or other primitives on a cron schedule. | Pro |
| [SLA Timer](/docs/primitives/sla-timer) | Escalate stuck jobs after a fixed duration. | Pro |
| [PDF Generator](/docs/primitives/pdf-generator) | Render PDFs from typst templates on transitions. | Pro |
| [Approval Gate](/docs/primitives/approval-gate) | Block transitions behind N-of-M approver votes. | Pro |
| [Field Compute](/docs/primitives/field-compute) | Compute virtual fields at read time using formulas. | Solo+ |
| [Field Mutator](/docs/primitives/field-mutator) | Atomically increment, decrement, or set a field — with bounds. | Solo+ |
| [Record Spawner](/docs/primitives/record-spawner) | Create entity records as a side effect of events. | Pro |
| [Cross-Entity Transition](/docs/primitives/cross-entity-transition) | Fire an FSM event on a related entity's job. | Pro |

## Trigger predicates

Every primitive that fires on entity changes can use an optional **[trigger predicate](/docs/primitives/trigger-predicates)** to filter exactly when it runs — fire on a boolean edge, on a threshold crossing, or only when a specific value appears.
