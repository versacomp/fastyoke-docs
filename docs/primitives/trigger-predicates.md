---
title: Trigger Predicates
description: Filter exactly when a primitive fires with an optional JSONLogic predicate over before/after/entity/now.
---

# Trigger Predicates

Every primitive bound to an `on_field_change`, `on_record_create`, or `on_transition` trigger can carry an optional `when:` predicate. The predicate is evaluated each time the trigger matches; the primitive only fires when the predicate returns true. This is how you express edges, threshold crossings, and value-specific filters without writing any code.

## The `when:` field

Add `when:` directly to the trigger object in your binding:

```toml
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
```

The predicate is plain [JSONLogic](https://jsonlogic.com) and runs in the same sandbox FSM guards use — fuel-bounded, no host imports, deterministic.

## What's in scope

The evaluator has four bound variables:

| Variable | Description |
|---|---|
| `before` | The entity payload *before* the change. Empty for `on_record_create`. |
| `after` | The entity payload *after* the change. Same as `entity` for `on_record_create`. |
| `entity` | The full entity payload at trigger time. For `on_transition`, this is the entity at the moment the transition fired. |
| `now` | The current time in epoch milliseconds, injected by the dispatcher. |

Use `{ var: "before.field" }` and `{ var: "after.field" }` to compare before-and-after values; `{ var: "entity.field" }` for direct payload reads.

## Preset shortcuts (Yoke Studio toolbar)

The Yoke Studio renders four preset buttons that produce the most common predicate shapes:

### Edge: false → true

```json
{
  "and": [
    { "==": [{ "var": "before.<field>" }, false] },
    { "==": [{ "var": "after.<field>" }, true] }
  ]
}
```

Fires only on the tick a boolean field flips from false to true. Use for "first time the order is marked paid" or "first time a checkbox is checked."

### Threshold crossed below

```json
{
  "and": [
    { ">=": [{ "var": "before.<field>" }, { "var": "threshold" }] },
    { "<": [{ "var": "after.<field>" }, { "var": "threshold" }] }
  ]
}
```

Fires only on the tick a numeric field crosses below a threshold. Use for inventory drop-below-reorder-point alerts.

### Threshold crossed above

```json
{
  "and": [
    { "<=": [{ "var": "before.<field>" }, { "var": "threshold" }] },
    { ">": [{ "var": "after.<field>" }, { "var": "threshold" }] }
  ]
}
```

Mirror image of "threshold crossed below." Use for budget overruns, capacity-met events.

### Value changed

```json
{ "!=": [{ "var": "before.<field>" }, { "var": "after.<field>" }] }
```

Fires only when the field value differs from the prior tick. Cheap general-purpose change detector.

## Validation

- The predicate must parse as JSONLogic at save time. Syntax errors are rejected with a 422.
- The predicate is fuel-bounded; a runaway predicate is treated as falsy and a `__primitive_predicate_error__` row is written to the event log.
- Degenerate predicates (always-true / always-false) are rejected at save time — they indicate a misconfiguration.

## When to use it

- Boolean edges: fire once on the false → true transition, not every subsequent write of `true`.
- Threshold crossings: alert on the tick a number crosses a limit, then stay quiet until it crosses back.
- Value-specific filters: fire only when `status` becomes `approved`, not when it churns through other values.

## What it can't do

- Reach outside `{ before, after, entity, now }`. The predicate has no host access.
- Block the trigger. The predicate filters whether the primitive fires; it doesn't abort the originating event.
- Replace [Field Compute](/docs/primitives/field-compute) for derived field math — that primitive is for read-time formulas, not trigger filtering.
