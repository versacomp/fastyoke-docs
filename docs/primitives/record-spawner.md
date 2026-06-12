---
title: Record Spawner
description: Create — or clear — an entity record (and optionally a paired job) as a deterministic side effect of an event.
---

# Record Spawner

The Record Spawner primitive creates an entity record when a trigger fires, using a deterministic id template so retries are idempotent. It can also delete that record when a paired `clear_when:` predicate becomes true — the "spawn-when-broken, clear-when-recovered" pattern — and can optionally spawn a paired FSM job in the same step.

## Configuration

| Field | Type | Description |
|---|---|---|
| `spawn_entity` | string | The entity to create. |
| `id_template` | string | Mustache-style template that produces the spawned record's id (e.g. `inv-alert-{{entity.id}}`). Determinism is what makes upserts safe. |
| `payload_template` | object | Mustache-rendered JSON object that becomes the spawned record's payload. |
| `on_collision` | enum | `upsert` (overwrite if exists), `skip` (no-op if exists), or `error`. |
| `clear_when` | JSONLogic object | Optional. When the predicate evaluates true, the spawned record is deleted instead of created. |
| `also_spawn_job` | object | Optional. `{ schema_name, initial_state }` — also creates a job bound to the spawned record. |

## Example: Inventory low-stock alert (spawn when below reorder point, clear when restocked)

```toml
[[bindings]]
primitive = "fastyoke.record_spawner"
trigger = {
  kind = "on_field_change",
  entity = "InventoryItem",
  field = "qty_on_hand",
  when = { "<": [{ var = "after.qty_on_hand" }, { var = "after.reorder_point" }] }
}
target_entity = "InventoryItem"
config = {
  spawn_entity = "InventoryAlert",
  id_template = "inv-alert-{{entity.id}}",
  payload_template = {
    item_id = "{{entity.id}}",
    on_hand = "{{after.qty_on_hand}}",
    reorder_point = "{{entity.reorder_point}}",
    status = "open"
  },
  on_collision = "upsert",
  clear_when = {
    ">=": [{ var = "after.qty_on_hand" }, { var = "after.reorder_point" }]
  }
}
```

When `qty_on_hand` drops below `reorder_point`, the engine upserts an `InventoryAlert` row with a deterministic id. When `qty_on_hand` rises back above `reorder_point` later, the same row is deleted. Retrying the original event is safe because the id is derived from the source record.

## When to use it

- Watchdog patterns: spawn an alert row when a condition goes bad, clear it when the condition recovers.
- Cascade workflows: spawn a follow-up record (and its job) as a side effect of a transition.
- Bulk materialization: pair with [Scheduler](/docs/primitives/scheduler) to run a periodic sweep that creates rows for everything matching a predicate.

## What it can't do

- Trigger on entity reads. Reads must not create records; only write-side triggers are supported.
- Resolve multi-hop FK paths. V1 paths are single-hop.
- Bypass the cascade budget. Each spawned record fires its own `on_record_create` bindings — the engine caps total invocations per originating event to keep accidental loops from running away.
