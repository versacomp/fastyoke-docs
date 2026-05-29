---
title: Simple Project Tracker
summary: Free marketplace app — organize projects, tasks, and comments with a Kanban board.
order: 3
---

# Simple Project Tracker

**Simple Project Tracker** is a free marketplace app that brings lightweight project management to your FastYoke workspace. Organize your work into projects, break them down into tasks, collaborate with comments, and visualize progress on a Kanban board.

## What it does

The app provides three core entities and everything you need to run them:

- **Projects** — top-level containers for related work. Each has a name, optional description, owner, and color for visual organization.
- **Tasks** — work items that live in projects (or stand alone as inbox-style items). Track title, description, assignee, priority, due date, and custom labels.
- **Comments** — threaded notes on tasks. Each comment carries author, body, and timestamps for team discussion.

All task state is managed through a **Kanban board** with five lanes: *Backlog*, *To Do*, *In Progress*, *Blocked*, and *Done*. Drag tasks between lanes to advance work. Unblock a task manually, reopen completed work if needed, or use the admin override to force a state change with a reason.

## Installing from the Marketplace

1. Go to the Marketplace (sidebar → **Marketplace**).
2. Find **Simple Project Tracker** in the free apps section.
3. Click **Install**.
4. Choose whether to load demo data (3 sample projects, 14 tasks, 6 comments). Demo mode helps you see the board in action; you can delete it anytime.
5. Done. The app appears in your workspace sidebar as **Projects**.

## In the app

Once installed, you'll see five main pages:

| Page | What it does |
|---|---|
| **Dashboard** | Quick overview — your active tasks and a list of all projects. |
| **Board** | Kanban view grouped by task state. Drag to transition; click a task to open its detail page. |
| **Projects** | Full list of projects. Click to open, or edit inline. |
| **Tasks** | Full list of tasks across all projects (and orphaned inbox items). Sort and filter by project, assignee, or priority. |
| **Comments** | Activity stream of all comments on all tasks. |

Click any task to open its **detail page**, where you can:

- Edit task fields (title, description, assignee, priority, due date, labels).
- See the full comment thread.
- Advance the task through its lifecycle using the state buttons.

## Building on it

The Kanban board and task lifecycle are powered by FastYoke's FSM Designer. You can extend the app without code:

- **Add custom guards** — e.g., "only move to Done if priority is not High" or "reject start if due date has passed". Edit the task lifecycle in **Admin** → **FSM Designer** → **task_lifecycle**.
- **Add custom fields** — go to **Admin** → **Annotation pages** and add fields to `task`, `project`, or `comment`. They appear immediately in forms and lists.
- **Customize page layout** — edit the app pages in **Admin** → **Pages** to hide fields, reorder columns, or tweak CSS.

## Multi-tenant and secure

Every tenant's projects, tasks, and comments are isolated. Uninstalling the app cleanly removes all seeded demo data while preserving the FSM schema (in case you create tasks later and want the same workflow).

## What's not included (v1)

- Attachments on tasks or comments.
- Due-date notifications or reminders.
- Time tracking or estimation.
- Sub-tasks.
- Real assignee management (currently free-text; integration with team members is a future enhancement).

These are deliberately simple starting points so you can focus on core workflow. If you need any of these, add them via the Annotation editor and the detail-page renderer will pick them up automatically.
