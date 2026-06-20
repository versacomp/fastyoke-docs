---
title: Dashboards
summary: Lay report tiles on a single page, choose a render shape per tile (number, bar, or line), and serve the dashboard as server-rendered HTML.
order: 2
---

# Dashboards

A **dashboard** is a collection of report tiles laid out on a single
page. Each tile points at a saved report and chooses a render shape.
Dashboards are tenant-scoped — every member of the tenant can view
them. There's no per-user dashboard variant today; one tenant
dashboard is one shared view.

## What a dashboard does

A saved dashboard owns a list of tiles. When you view the dashboard
in the admin shell, the platform fetches each tile's report,
executes it, and renders the result in the tile's chosen shape.
When you fetch the dashboard's render endpoint, the platform
produces the same view as server-side HTML — that's the bytes a
scheduled [digest](/docs/reports/digests) emails out.

## Add a tile

From a saved report, choose three things:

- **Title** — the human label that appears above the tile.
- **Render** — one of:
  - `number` — a single big value. Works for any aggregate
    without a group-by (or with a single-row group-by result).
  - `bar` — a grouped bar chart. Requires the underlying report
    to have a group-by; each bar is one `GroupValue`.
  - `line` — a time-series line. Requires group-by on a
    date-typed field; the platform sorts the groups
    chronologically.
- **Width** — how many grid columns the tile spans on the
  dashboard.

The three render shapes are the complete set today; no `pie`, no
`table`, no `gauge`. A report can be tiled multiple times across
multiple dashboards in different shapes.

## Render the dashboard

```http
GET /api/v1/tenant/dashboards/:id/render
```

Returns server-rendered HTML — every tile, every chart, inlined and
ready to email. The admin UI's dashboard view uses the same
endpoint. Scheduled [digests](/docs/reports/digests) use it too:
the email body is the same HTML the admin UI shows.

## Per-tier caps

Dashboards are gated the same way as reports:

- **Hobby**: cannot create dashboards.
- **Pro: 5** dashboards per tenant.
- **Team: 25** dashboards per tenant.
- **Enterprise / Fleet**: unlimited.

Caps are per-tenant and count separately from reports.

## API reference

```http
POST /api/v1/tenant/dashboards
GET /api/v1/tenant/dashboards
GET /api/v1/tenant/dashboards/:id
PATCH /api/v1/tenant/dashboards/:id
DELETE /api/v1/tenant/dashboards/:id
GET /api/v1/tenant/dashboards/:id/render
```

`render` returns HTML; everything else returns JSON.

## See also

- [Reports](/docs/reports) — build the reports that tile onto a
  dashboard.
- [Digests](/docs/reports/digests) — email this dashboard on a
  schedule.
