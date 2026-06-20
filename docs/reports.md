---
title: Reports & Dashboards
summary: Aggregate entity records into single numbers or grouped rollups, lay them out on tenant-shared dashboards, and email digests on a schedule.
order: 1
---

# Reports & Dashboards

This section covers reports (aggregations over your entity records),
saved dashboards (tile layouts of those reports), and scheduled
digests (recurring email delivery of a dashboard). The three pages
build on each other: a dashboard groups tiles built from saved
reports, and a digest delivers a dashboard on cadence.

## What a report does

A **report** aggregates entity records into a number, or a list of
numbers grouped by a field. You pick the source entity, an optional
filter, an optional group-by, and an aggregate function. The result
is JSON when you fetch it programmatically; a tile on a dashboard
when you render it.

## Build a report

Open **Reports** in the admin shell and choose:

- **Entity** — which entity's records to scan.
- **Filter** (optional) — `field=value AND field2=value2`
  expression. Only records matching every clause count. Same
  filter syntax used elsewhere on the platform.
- **Group by** (optional) — split into one row per distinct value
  of a field. Leave empty for a single total. The synthetic
  `current_state` field is supported — group by workflow state
  without modeling it as a real field.
- **Aggregate** — how to combine records in each group. Five
  operations, all wire-format names:

| Op | Result | `aggregate_field` required? |
|---|---|---|
| `count` | Number of matching records | no |
| `sum` | Sum of a numeric field | yes |
| `avg` | Mean of a numeric field | yes |
| `min` | Smallest value of a field | yes |
| `max` | Largest value of a field | yes |

Note the wire format names — the platform speaks `avg`, not
`average`; `min`, not `minimum`.

## Run a report

From the admin UI or `POST /api/v1/tenant/reports/:id/run`. The
response is a `ReportResult` — a list of `GroupValue` rows with
`key` (the group value, or `null` for an ungrouped total) and
`value` (the aggregate result).

A report can be re-run as often as you like; results aren't cached
between calls. For recurring delivery, lay the report on a
dashboard and schedule a [digest](/docs/reports/digests).

## Per-tier caps

Reports are gated per tier:

- **Hobby**: cannot create reports.
- **Pro: 5** reports per tenant.
- **Team: 25** reports per tenant.
- **Enterprise / Fleet**: unlimited.

Caps are per-tenant and count separately from dashboards — a Pro
tenant can hold 5 reports AND 5 dashboards.

## API reference

```http
POST /api/v1/tenant/reports
GET /api/v1/tenant/reports
GET /api/v1/tenant/reports/:id
PATCH /api/v1/tenant/reports/:id
DELETE /api/v1/tenant/reports/:id
POST /api/v1/tenant/reports/:id/run
```

Create / get / patch / delete operate on the report definition;
`run` executes the report and returns a `ReportResult`.

## See also

- [Dashboards](/docs/reports/dashboards) — lay tiles built from
  reports onto a tenant-shared page.
- [Digests](/docs/reports/digests) — email a dashboard on cadence
  to a list of recipients.
