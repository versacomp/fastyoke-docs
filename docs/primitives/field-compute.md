---
title: Field Compute
description: Compute a virtual field at read time using a sandboxed formula — no stored column, no extra writes.
---

# Field Compute

The Field Compute primitive adds a virtual field to an entity, computed at read time from a small JavaScript-style formula. Nothing is stored on disk — the computation runs every time the entity is read, in a fuel-bounded sandbox.

## Configuration

| Field | Type | Description |
|---|---|---|
| `target_entity` | string | The entity whose read response gains the virtual field. |
| `field` | string | The virtual field name (must not collide with a stored column). |
| `formula` | string | A short expression in the sandboxed evaluator (e.g. `entity.total - entity.amount_paid`). |
| `mode` | enum | V1 supports only `merge` — the result is merged into the response object under `field`. |
| `fuel_budget` | integer | Optional. Per-evaluation instruction ceiling; defaults to 10,000, capped at 100,000. |

## Example: Balance due on an Order

```toml
[[bindings]]
primitive = "fastyoke.field_compute"
trigger = { kind = "on_entity_read", entity = "Order" }
target_entity = "Order"
config = {
  field = "balance_due",
  formula = "entity.total - entity.amount_paid",
  mode = "merge"
}
```

Every time an Order is read, the engine evaluates `entity.total - entity.amount_paid` inside the sandbox and adds the result to the response under `balance_due`. Bulk list reads cache the binding lookup so a 100-row response evaluates the formula 100 times but reads the binding configuration once.

## When to use it

- Derived totals that don't warrant a write column (`balance_due`, `is_overdue`, `days_open`).
- Aggregating a couple of stored fields into a single display value.
- Boolean flags computed from payload state (e.g. `entity.shipped && !entity.invoiced`).

## What it can't do

- Persist the computed value. For "writeback on event" use [Aggregator](/docs/primitives/aggregator) or [Field Mutator](/docs/primitives/field-mutator).
- Reach across entities or talk to the network. The sandbox has no host imports; only `entity` is bound.
- Run unbounded code. Formulas are fuel-capped (default 10k instructions) and memory-capped (1 MiB).
- Take a non-deterministic input. Same `entity` always produces the same output.
