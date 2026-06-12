---
title: Aggregator
description: Roll up child records into a parent field using sum, count, average, min, or max — with optional row filtering.
---

# Aggregator

The Aggregator primitive computes a value over child records of a parent and writes the result to a parent field. It supports five reducers (sum, count, avg, min, max) and an optional `where:` filter so you can compute aggregates over a subset of children.

## Configuration

| Field | Type | Description |
|---|---|---|
| `source_entity` | string | The child entity name (e.g. `OrderLine`). |
| `parent_entity` | string | The parent entity name (e.g. `Order`). |
| `parent_link_field` | string | The field on the child that holds the parent's id (e.g. `order_id`). |
| `aggregate` | enum | One of `sum`, `count`, `avg`, `min`, `max`. |
| `source_field` | string | The child field to aggregate. Required unless `aggregate=count`. |
| `target_field` | string | The parent field that receives the result. |
| `where` | JSONLogic object | Optional. Only child rows where the predicate evaluates true are included. |

## Example: Sum of approved-only line items

```toml
[[bindings]]
primitive = "fastyoke.aggregator"
trigger = { kind = "on_field_change", entity = "OrderLine", field = "amount" }
config = {
  source_entity = "OrderLine",
  parent_entity = "Order",
  parent_link_field = "order_id",
  aggregate = "sum",
  source_field = "amount",
  target_field = "approved_total",
  where = { "==": [{ var = "status" }, "approved"] }
}
```

When any `OrderLine.amount` changes, Aggregator recomputes the sum of `amount` across all `OrderLine` rows linked to the same `Order` where `status == "approved"`, and writes the result to `Order.approved_total`.

## When to use it

- Order totals from line items.
- Payment running totals from a payment ledger (combined with `where:` filter on status).
- Inventory on-hand summed across all warehouses for a single SKU.

## What it can't do

- Atomic delta-write — Aggregator recomputes from children. For "add 5 to this counter" use [Field Mutator](/docs/primitives/field-mutator).
- Cross-entity lookup — Aggregator works on a single parent-child relationship. To denormalize a value from a different entity, see [Field Compute](/docs/primitives/field-compute).
