---
title: Studio
description: Design apps and Yokes in a persistent workspace — drafts autosave and resume across sessions.
---

# Studio

Studio is where you design an app or a multi-board Yoke from scratch, from a
vertical template, from a clone of an existing app, or from a short written
brief. It walks you through choosing a layout, branding, entities, boards, and
engine bindings, then activates the result into your workspace.

## Drafts autosave and resume

Studio work lives as a **draft**. Your changes **save automatically** as you
edit — there is no "save" button to remember, and reloading the page or coming
back tomorrow picks up exactly where you left off.

Open **Studio** from the admin sidebar to see all of your workspace's drafts:

- Each draft shows its name, status, and when it was last edited (and by whom).
- **Resume** any draft to keep working on it.
- **Delete** drafts you no longer need.
- **New Studio app** starts a fresh draft and drops you into the designer.

Drafts are visible to every admin in your workspace, so a teammate can pick up
a draft you started. Edits save last-writer-wins — Studio does not support two
people editing the same draft at the same moment.

## Activating

When a draft is ready, **activate** it from the final review step. Activation
creates the theme and installs the app/Yoke into your workspace; the draft is
then marked **activated** and kept as a record. Activating does not delete the
draft.

## Archiving drafts

To keep your Studio list focused, **archive** drafts you're done with — they
move out of the **Active** list into an **Archived** view, but nothing is
deleted. Archived drafts (and their version history) are kept and can be
**restored** at any time.

Activating a draft archives it automatically: once it's installed into your
workspace its work is done, so it moves to **Archived** as a record. Switch to
the **Archived** tab to restore a draft or, if you really don't need it,
permanently delete it (which also removes its version history).

## Version history

Studio keeps a history of your draft so you can checkpoint and roll back.

- **Save a snapshot** at any time from the **Version history** panel — give it a
  name (for example, "before the pricing rework") and it captures the draft
  exactly as it is right then.
- Studio also snapshots **automatically when you activate** a draft, so every
  activation has a matching record of what shipped.
- **Restore** any snapshot to roll the working draft back to that point. Restore
  is safe: before it replaces your current draft, Studio saves a backup snapshot
  of the current state, so you can always undo a restore.
- **Diff vs current** shows exactly which fields changed between a snapshot and
  the draft you're editing now.
- **Delete** snapshots you no longer need. Deleting a draft removes its history
  along with it.

Snapshots are visible to every admin in your workspace, the same as drafts.

## Who can use Studio

Studio is available to workspace admins with app-install permission.
