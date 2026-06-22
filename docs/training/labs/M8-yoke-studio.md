---
title: Lab M8 — Yoke Studio
summary: Build a 2-board Yoke in Yoke Studio at /admin/studio, capture a named snapshot, edit, restore. Confirm the restore reverts your edit.
order: 16
---

# Lab M8 — Yoke Studio

This lab pairs with **Module M8 — Yoke Studio** in the
[syllabus](/docs/training/syllabus).

## What you'll do

Build a 2-board Yoke in Studio. Capture a named snapshot.
Edit one board (rename or add a block). Restore the
snapshot. Confirm the edit reverts.

## Before you begin

- An admin session on a tenant you're an admin of.
- About 30 minutes.

## Or use Postman

This lab is GUI work in the admin shell. Postman doesn't apply here — open Yoke Studio at `/admin/studio` and
follow the steps below.

## Steps

1. **Open Yoke Studio.** Navigate to `/admin/studio`.

   **Checkpoint:** the Studio surface loads with a
   "New Yoke" affordance.
2. **Create a new Yoke draft.** Name it
   `Maintenance Tracker`.

   **Checkpoint:** the draft appears in the Studio
   drafts list.
3. **Add Board 1 — Entities.** Add a board surfacing an
   entity (use `vehicle` from M3 or any tenant entity).

   **Checkpoint:** Board 1 renders the entity list.
4. **Add Board 2 — Workflow.** Add a board surfacing a
   workflow schema (use the `service_ticket` from M5 or
   any tenant FSM).

   **Checkpoint:** Board 2 renders the workflow's state
   list.
5. **Capture a named snapshot.** From the Studio menu →
   **Snapshots → New snapshot**. Name it `v1-baseline`.

   **Checkpoint:** the snapshot appears in the snapshot
   list with the name and a timestamp.
6. **Edit the Yoke.** Rename Board 1 (or add a third
   block — anything observable).

   **Checkpoint:** the edit shows in the draft.
7. **Restore the snapshot.** From the snapshot list →
   **Restore `v1-baseline`**.

   **Checkpoint:** the edit reverts; the draft is back
   to what `v1-baseline` captured. Studio prompts before
   destroying current changes — confirm.

## What you'll have at the end

A Studio draft with a named snapshot, one round-trip of
edit + restore proving the snapshot model works.

## Stuck?

See [Lab M8 — Solution](/docs/training/labs/M8-yoke-studio-solution).

## Next

[Lab M9 — Extensions](/docs/training/labs/M9-extensions).
