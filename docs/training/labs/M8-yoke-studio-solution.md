---
title: Lab M8 — Solution
summary: Reference solution for Lab M8 — Studio menu paths plus three common gotchas around explicit-snapshot semantics, destructive restore, and per-tenant draft ownership.
order: 17
---

# Lab M8 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M8](/docs/training/labs/M8-yoke-studio). Work the lab
first — the friction of clicking through is intentional.
::

## Answer per step

### Step 1 — Open Studio

The full URL is `/admin/studio`. The surface loads inside
the admin shell.

### Step 2 — Create a draft

**`/admin/studio` → "New Yoke"**. Name accepts free text.
Draft is autosaved from the first keystroke; you can leave
and return without losing progress.

### Step 3 — Add Board 1

**Studio → Add Board → Entity**. Pick the entity slug.
The board renders against live tenant data.

### Step 4 — Add Board 2

**Studio → Add Board → Workflow**. Pick the schema slug.
The board lists the workflow's states.

### Step 5 — Capture snapshot

**Studio → Snapshots → New snapshot**. Name it
`v1-baseline`. The snapshot list shows it immediately.

### Step 6 — Edit

Rename a board or add a block. The edit is autosaved.

### Step 7 — Restore

**Studio → Snapshots → Restore**. Studio prompts to
confirm — restore overwrites the current in-progress
edit. Click through, and the draft reverts.

## Common gotchas

- **Studio autosaves the draft, but snapshot creation is
  explicit.** Until you click "New snapshot" there's no
  restore point. The autosaved draft is "current state",
  not "captured state".
- **Restore is destructive.** It overwrites your current
  in-progress edit. If the edit is worth keeping,
  capture a fresh snapshot before restoring.
- **The draft is per-tenant; multiple admins editing the
  same draft last-writer-wins.** No collaborative
  multi-user editing in Studio today.

## Back to the lab

[Lab M8 — Yoke Studio](/docs/training/labs/M8-yoke-studio).
