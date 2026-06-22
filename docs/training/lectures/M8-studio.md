---
title: Lecture M8 — Yoke Studio
summary: Why Studio uses persistent drafts with snapshots, the draft-archive lifecycle, and what "edit a published Yoke in place" does behind the scenes.
order: 9
---

# Lecture M8 — Yoke Studio

## The mental model

Yoke Studio is the authoring environment for **Yokes** —
publishable bundles of entities, schemas, FSMs, pages, and
apps. Studio holds your work as a **persistent draft**:
autosaved, resumable across sessions, snapshot-able for
checkpoint/restore. When you're ready, you **activate**
the draft into the tenant's runtime.

## Key concepts

- **Studio draft.** Tenant-scoped, opaque JSON payload up
  to 512KB. Autosaves with last-writer-wins semantics. One
  active draft per learner.
- **Snapshots.** Named checkpoints of a draft. Restore is
  transactional. Activating a draft auto-snapshots first.
- **Archive lifecycle.** Drafts can be archived (hidden
  from the active list) and unarchived. Activating
  auto-archives — there's no auto-delete. Pruning is the
  operator's call.
- **Edit-in-place via in-place re-version.** Editing a
  published Yoke loads its state into Studio,
  preserves the slug, and re-versions on save. No
  clone-and-promote round trip.
- **Studio drafts are *not* the runtime.** Until you
  activate, your changes are author-time only. The runtime
  keeps serving the previous version.

## Common pitfalls

- **Treating the active list as version history.**
  Snapshots are the history. The active list is the
  workbench.
- **Editing the same draft from two browser tabs.**
  Last-writer-wins; the slower tab loses its changes
  silently. Use snapshots if you need a fork.
- **Activating without a snapshot.** Studio auto-snapshots
  on activate — but if you've been ignoring snapshots,
  you have only the auto-snapshot to roll back to. Take
  named ones at meaningful milestones.

## Where to go next

- Lab: [M8 — Yoke Studio](/docs/training/labs/M8-yoke-studio).
- Reference: [/docs/apps/studio](/docs/apps/studio).
