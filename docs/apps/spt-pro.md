---
title: Project Tracker Pro — Timeline
summary: Pro add-on for Simple Project Tracker — Waterfall/Gantt timeline with task bars, dependencies, critical path, milestones, and % complete.
order: 4
---

# Project Tracker Pro — Timeline

**Project Tracker Pro** is a Pro-tier add-on that extends [Simple Project Tracker](/docs/apps/simple-project-tracker) with a Waterfall/Gantt timeline view. It gives project teams a date-oriented picture of every task alongside the Kanban board already in the base app.

## What the Timeline adds

The **Timeline** page renders each task as a horizontal bar spanning its start and due date. At a glance you can see:

- **Task bars** — each bar is labeled with the task name and colored by project. Bar width reflects the calendar span between start date and due date.
- **Dependencies** — arrows connect tasks that must finish before the next one can start, so you can see downstream knock-on effects immediately.
- **Critical path** — the longest dependency chain is highlighted so you know which tasks, if delayed, push the overall project end date.
- **Milestones** — tasks marked as milestones appear as diamond markers rather than bars, for clear delivery-point visibility.
- **% complete** — a fill indicator inside each bar shows how far along the task is based on its completion percentage field.

## Requirements

- **Simple Project Tracker** must already be installed. Project Tracker Pro layers on top of it and reads the same projects and tasks.
- A **Pro workspace subscription** is required. The listing is visible in Logic Cores on all plans, but installation requires Pro or above.

## Installing

1. Go to **Logic Cores** (sidebar).
2. Find **Project Tracker Pro** under the Pro add-ons section.
3. Click **Install**. The Timeline page is added to your Projects app immediately — no data migration required.
4. Optionally load demo data: a set of sample tasks with start/due dates, dependencies, and milestones is seeded so you can explore the view right away.

## Using the Timeline

Open **Projects** → **Timeline** in your workspace sidebar. The view loads all tasks that have both a start date and a due date set. Tasks missing either date appear in a holding list at the bottom so you can fill in the dates and have them join the chart.

Pan left/right to scroll through the calendar. Zoom controls adjust the scale between day-level and month-level granularity. Click any bar to open the task's detail page.

## Extending it

The Timeline block respects the same FSM lifecycle as the Kanban board — advancing a task to Done via either view updates the same underlying state. You can add custom fields (via **Admin** → **Annotation pages**) that appear in the task detail panel opened from the timeline.

## What's not included

- Resource leveling or workload views.
- Baseline/actual tracking or earned-value metrics.
- Export to MS Project or other Gantt formats.
