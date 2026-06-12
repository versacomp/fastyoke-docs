---
title: Field Mutator
description: Atomically add to, set, or bound-check a single field on a target entity — the right tool for inventory counts and lifetime quotas.
---

# Field Mutator

The Field Mutator primitive performs an atomic field-level update on a target entity in response to a trigger. It can add a delta, set a value, or enforce minimum and maximum bounds — and the bound check can run pre-commit so a violation aborts the originating event.

## Configuration

| Field | Type | Description |
|---|---|---|
| `target_entity` | string | Entity whose field is being mutated. |
| `target_lookup_path` | string | Single-hop path on the trigger entity that resolves to the target's id (e.g. `entity.item_id`). |
| `field` | string | The field on `target_entity` to update. |
| `delta_source` | string | Path on the trigger entity that supplies the delta or set value (e.g. `entity.quantity`). |
| `delta_op` | enum | `add` (delta is added to the current value) or `set` (delta replaces the current value). |
| `min_bound` | number | Optional lower bound. Combine with `enforce_bounds=true` for hard nonnegative invariants. |
| `max_bound` | number | Optional upper bound. |
| `enforce_bounds` | boolean | `true` runs pre-commit (a violation rolls back the originating event with 422). `false` runs post-commit (a violation no-ops the mutation and writes an audit row, but the originating event still succeeds). |
| `storage_scope` | enum | `entity` (default — mutates `target_entity.data_payload[field]`) or `tenant_counter` (mutates a tenant-wide counter keyed by your Yoke slug + a key you supply — used for lifetime quotas). |

## Example: Decrement inventory on a stock movement (nonnegative-enforced)

```toml
[[bindings]]
primitive = "fastyoke.field_mutator"
trigger = { kind = "on_record_create", entity = "StockMovement" }
target_entity = "InventoryItem"
config = {
  target_lookup_path = "entity.item_id",
  field = "qty_on_hand",
  delta_source = "entity.quantity",
  delta_op = "add",
  min_bound = 0,
  enforce_bounds = true
}
```

When a `StockMovement` is created, the engine atomically updates `InventoryItem.qty_on_hand` by the movement's `quantity`. If the result would drop below zero, the originating create fails with a 422 — your `StockMovement` is never written, your inventory never goes negative.

## When to use it

- Inventory increment / decrement with a nonnegative invariant.
- Counter primitives where you don't want to recompute from children (the [Aggregator](/docs/primitives/aggregator) anti-case).
- Free-tier lifetime quotas (`storage_scope = "tenant_counter"`) — declarative replacement for hand-rolled cap tables.

## What it can't do

- Multi-hop FK resolution. V1 supports `entity.<field>` only; lookups like `entity.customer.account_id` are deferred.
- Multiple mutators on the same `(entity, field)` pair. The validator rejects this at save time to avoid write-conflict races.
- Cross-tenant counters. Counters are always scoped by tenant and Yoke slug.
- Run unbounded operations. Each atomic update is bounded by the per-statement timeout.
