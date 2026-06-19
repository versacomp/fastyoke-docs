---
title: Reports & Dashboards
description: Build saved reports over your data, lay them out on dashboards, and schedule email digests.
---

# Reports & Dashboards

A **report** aggregates your entity records: pick an entity, an optional filter
(`field=value AND …`), an optional group-by field, and an aggregate
(count / sum / average / min / max). You can group by any field your records
store, plus the synthetic `current_state` field (a record's workflow state).

## Building a report

Choose:

- **Entity** — which entity's records to scan.
- **Filter** *(optional)* — a `field=value AND field2=value2` expression. Only
  records matching every clause are counted. The same filter syntax used
  elsewhere in FastYoke applies here.
- **Group by** *(optional)* — split the result into one row per distinct value
  of a field. Leave it empty for a single total.
- **Aggregate** — how to combine the records in each group:

  | Aggregate | Result                                              |
  |-----------|-----------------------------------------------------|
  | `count`   | Number of matching records.                         |
  | `sum`     | Sum of a numeric field across matching records.     |
  | `average` | Mean of a numeric field across matching records.    |
  | `min`     | Smallest value of a field across matching records.  |
  | `max`     | Largest value of a field across matching records.   |

Reports are computed fresh each time they are read, so the numbers always
reflect your current data.

### The `current_state` field

Every report can group by or filter on `current_state` — a synthetic field that
exposes a record's workflow state. Use it to answer questions like "how many
jobs are in each state" without storing the state on the record yourself.

## Dashboards

A **dashboard** lays reports out as an ordered grid of tiles. Each tile points
at one report and renders it in one of three ways:

- **Number** — a single headline value (best for an ungrouped aggregate).
- **Bar** — one bar per group (best for a grouped count or sum).
- **Line** — a line across groups (best for a trend over an ordered group-by).

Add, reorder, and remove tiles from the dashboard editor. A report can appear on
more than one dashboard.

## Scheduled digests

Pro plans and above can schedule a dashboard to email its tile values on a
**daily** or **weekly** cadence to a list of **recipients**. The digest renders
each tile's current value as an HTML email. A dashboard sends at most one digest
per day, so re-running the scheduler within the same day will not double-send.

## Limits

Reports scan up to **50,000 records**; beyond that a result is marked as showing
the first 50,000 rows (the `truncated` flag). Group by a higher-cardinality
field or tighten your filter to stay under the cap.

Report and dashboard counts are limited by plan, and scheduling requires a Pro
plan or above:

| Plan             | Reports | Dashboards | Scheduled digests |
|------------------|---------|------------|-------------------|
| Hobby            | 0       | 0          | —                 |
| Pro              | 5       | 5          | Yes               |
| Team             | 25      | 25         | Yes               |
| Enterprise/Fleet | ∞       | ∞          | Yes               |

Reports and dashboards are counted separately against your plan's caps.
