---
title: Quiz M8 — Yoke Studio
summary: 10 multiple-choice questions on persistent drafts, snapshots, the archive lifecycle, and edit-in-place re-versioning.
order: 9
---

# Quiz M8 — Yoke Studio

Pairs with [Lecture M8](/docs/training/lectures/M8-studio)
and [Lab M8](/docs/training/labs/M8-yoke-studio).

**Q1.** What's a Studio draft?

a) A staging area that auto-promotes after timeout
b) A tenant-scoped, autosaved JSON payload (up to 512KB)
c) An immutable snapshot
d) A branch of the live Yoke

<details>
<summary>Answer + why</summary>

**b)** Resumable across sessions, opaque JSON, capped at
512KB.

</details>

**Q2.** What does activating a draft trigger automatically?

a) A snapshot of the previous state
b) A factory reset of the App
c) A migration of historical entity rows
d) Nothing — pure activate

<details>
<summary>Answer + why</summary>

**a)** Auto-snapshot before activate gives you a rollback
point even if you've been ignoring snapshots.

</details>

**Q3.** What's a Studio snapshot's restore semantics?

a) Eventual consistency
b) Transactional — all or nothing
c) Snapshot-then-merge
d) Replaces just the schema

<details>
<summary>Answer + why</summary>

**b)** Restore is transactional so a failed restore leaves
the draft untouched.

</details>

**Q4.** Editing the same draft from two browser tabs results in…

a) A conflict prompt
b) Last-writer-wins; the slower tab loses changes silently
c) A merged result
d) The second tab gets a read-only lock

<details>
<summary>Answer + why</summary>

**b)** Optimistic concurrency. Use snapshots if you need
to fork.

</details>

**Q5.** What does "edit a published Yoke in place" do?

a) Edits the live runtime directly
b) Loads the Yoke into Studio, preserves the slug, re-versions on save
c) Clones to a new slug
d) Pauses the runtime

<details>
<summary>Answer + why</summary>

**b)** In-place re-versioning — no clone-and-promote
round trip.

</details>

**Q6.** Archived drafts are…

a) Auto-deleted after 30 days
b) Hidden from the active list; can be unarchived
c) Read-only
d) Cross-tenant shareable

<details>
<summary>Answer + why</summary>

**b)** No auto-delete. Pruning is the operator's call.

</details>

**Q7.** What happens when you activate a draft?

a) It auto-archives
b) It stays in the active list as the "live" entry
c) It is deleted
d) It's marked read-only

<details>
<summary>Answer + why</summary>

**a)** Auto-archive on activate is the canonical
lifecycle.

</details>

**Q8.** Where's the right place to think about "version history"?

a) The active draft list
b) Named snapshots
c) Git
d) The event log

<details>
<summary>Answer + why</summary>

**b)** Snapshots are the history; the active list is the
workbench.

</details>

**Q9.** Studio drafts are…

a) Live in the runtime
b) Author-time only until activated
c) Visible to end customers immediately
d) Shared across tenants

<details>
<summary>Answer + why</summary>

**b)** Until activate, the runtime serves the previous
version.

</details>

**Q10.** Can you have multiple active drafts per learner?

a) Yes — unlimited
b) One active draft per learner; archive an old one before starting a new one
c) Two — current + previous
d) Only on Enterprise

<details>
<summary>Answer + why</summary>

**b)** One slot. Forks live as snapshots, not as parallel
active drafts.

</details>
