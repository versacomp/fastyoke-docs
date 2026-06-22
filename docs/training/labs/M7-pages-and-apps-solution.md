---
title: Lab M7 — Solution
summary: Reference solution for Lab M7 — menu paths per step plus three common gotchas around theme cascade, dashboard widget refresh, and draft-form embeds.
order: 15
---

# Lab M7 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M7](/docs/training/labs/M7-pages-and-apps). Work the
lab first — the friction of clicking through is
intentional.
::

## Answer per step

### Step 1 — Create the App

**Settings → Apps → New App**. Name field accepts free
text. The App appears in the Apps list with a default
icon.

### Step 2 — Theme override

**App Settings → Theme → Override**. The override surface
exposes the brand color tokens; flipping one of them
re-renders the App's edit shell immediately.

### Step 3 — Vehicles list page

**App → Pages → Add Page → Entity list block**. Pick
`vehicle` as the entity. The preview pulls live data.

### Step 4 — Intake form page

**App → Pages → Add Page → Form block**. Pick the form
you composed in M4.

### Step 5 — Dashboard widget

**App → Pages → Add Page → Dashboard widget**. The
count-of-`vehicle` widget surfaces the same data as
`GET /entities/vehicle?count=true`.

### Step 6 — Navigation order

**App → Navigation → Drag to reorder**. Saves on drop;
no explicit "save" button.

### Step 7 — Open the App

The App's URL is rendered in the admin shell sidebar
under the App name.

## Common gotchas

- **Theme overrides cascade.** Child pages inherit the
  override; sibling Apps do not. Each App owns its theme.
- **The dashboard widget reads the entity-count endpoint
  at render time.** If the count looks wrong after you
  add a record, refresh the page — there's no live
  invalidation.
- **If the form embed renders empty,** the form may still
  be in draft state. Open Forms, publish it, and refresh
  the App.

## Back to the lab

[Lab M7 — Pages & App Builder](/docs/training/labs/M7-pages-and-apps).
