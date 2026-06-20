---
title: Scheduled digests
summary: Attach a schedule to a dashboard and the platform emails the rendered dashboard to a list of recipients on cadence. Daily or weekly; UTC clock; Pro tier or higher.
order: 3
---

# Scheduled digests

A **digest** is a schedule attached to a dashboard. The platform
renders the dashboard on cadence and emails the result to a list of
recipients. The email body is the same HTML the dashboard's render
endpoint produces.

## Configure a schedule

A digest's schedule object has four fields:

- **`cadence`** — `daily` or `weekly`. These are the only two
  cadences today.
- **`hour_utc`** — integer 0–23. **The platform clocks digests in
  UTC**, not the tenant's local timezone. Pick the hour that
  matches when you want the email to arrive in your local time —
  account for your local offset (a tenant in UTC−5 wanting an 8am
  local digest sets `hour_utc: 13`).
- **`weekday`** — integer 0–6 with **Mon=0**, Sun=6. Required when
  cadence is `weekly`; ignored when cadence is `daily`.
- **`recipients`** — list of email addresses. Any number; no
  per-recipient personalization — everyone gets the same render.

## When a digest fires

A platform scheduler runs hourly. For each dashboard with a
schedule, the scheduler checks two predicates:

1. `now.hour_utc == schedule.hour_utc`
2. For weekly: `now.weekday == schedule.weekday`

If both match, the digest sends. The platform tracks `last_sent_at`
per dashboard so a digest can't double-fire within the same hour
even if the scheduler tick overlaps the boundary.

If the dashboard's render endpoint fails at digest time, no email
goes out for that tick; the next scheduled tick re-attempts.

## Tier gating

**Scheduling a digest requires Pro tier or higher.** Hobby tenants
can build reports and dashboards but cannot attach a schedule; the
admin UI disables the schedule editor for Hobby and the API
rejects the schedule field on save.

Once a digest is scheduled, every recipient on the list receives
the email regardless of tenant membership. Recipients are
addresses, not user references — you can email anyone with a
mailbox.

## What's in the email

- **Subject** — names the dashboard.
- **Body** — the HTML the render endpoint produces, embedded
  inline. Charts render with the same shapes as the admin UI view;
  widths follow the tile configuration.
- **No external assets.** Everything renders inline so the email
  works in mail clients that block remote content by default.

## Pause or remove a digest

Two options:

- **Clear the schedule** — set the dashboard's `schedule` field to
  `null`. The dashboard stays; emails stop. Re-add a schedule any
  time to resume.
- **Delete the dashboard** — removes both the layout and the
  schedule. Recipients receive nothing further.

## See also

- [Reports](/docs/reports) — the reports that tile onto the
  dashboard.
- [Dashboards](/docs/reports/dashboards) — the layout the digest
  emails.
