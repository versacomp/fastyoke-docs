---
title: Cross-Entity Transition
description: Fire an FSM event on a related entity's job — the declarative replacement for hand-rolled cascade calls.
---

# Cross-Entity Transition

The Cross-Entity Transition primitive fires an FSM event on a job belonging to a *different* entity from the one that triggered it. It's the declarative way to express "when an Order is marked paid, advance the related Customer's lifecycle" — the cascade that today is hand-rolled in several verticals.

## Configuration

| Field | Type | Description |
|---|---|---|
| `fk_path` | string | Single-hop path on the trigger entity that resolves to the target record's id (e.g. `entity.customer_id`). |
| `target_schema_name` | string | The FSM schema bound to the target entity (e.g. `customer_lifecycle`). |
| `target_entity_name` | string | The target entity's name. |
| `event_name` | string | The FSM event to fire on the target's job. |
| `idempotency` | enum | `skip_if_current_state_matches_post_transition` (default — no-op if the target is already in the post-event state) or `always_fire`. |
| `on_concurrent_conflict` | enum | `swallow` (default — return Ok on a Conflict, matching how all today's hand-rolled callers behave) or `error`. |

## Example: When an Order is paid, advance its Customer to active

```toml
[[bindings]]
primitive = "fastyoke.cross_entity_transition"
trigger = {
  kind = "on_field_change",
  entity = "Order",
  field = "paid",
  when = {
    "and": [
      { "==": [{ var = "before.paid" }, false] },
      { "==": [{ var = "after.paid" }, true] }
    ]
  }
}
target_entity = "Order"
config = {
  fk_path = "entity.customer_id",
  target_schema_name = "customer_lifecycle",
  target_entity_name = "Customer",
  event_name = "go_live",
  idempotency = "skip_if_current_state_matches_post_transition",
  on_concurrent_conflict = "swallow"
}
```

When an Order's `paid` field flips from false to true, the engine resolves the Order's `customer_id`, finds the Customer's lifecycle job, and fires `go_live` against it. The default idempotency setting means firing the same trigger again is a no-op once the customer is already live. Concurrent-conflict swallowing matches the behavior of the hand-rolled cascades in payments, urgent care, and CRM conversions.

## When to use it

- Lifecycle promotion: paying an Order activates the linked Customer, marking a Visit paid checks the patient out.
- Quote → Order → Opportunity cascades, where advancing one job needs to advance another.
- Any "if A then advance B" rule that today lives in cross-table backend code.

## What it can't do

- Resolve multi-hop FK paths. V1 supports `entity.<field>` only; deeper paths are deferred.
- Skip the target's guards. The target FSM's guards still run — `apply_transition` is invoked exactly as if a human had fired the event.
- Run pre-commit. Cross-entity transitions always fire after the originating event commits, so the user's action succeeds even if the cascade can't.
- Survive a schema rename. If the target schema disappears or its event is removed between save and runtime, the engine logs a schema-drift audit row and skips the cascade.
