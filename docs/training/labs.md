---
title: About the labs
summary: Hands-on lab exercises pinned to syllabus modules. Two labs per week through M16. This page documents the uniform six-section lab scaffold and the three-section solution scaffold every lab follows.
order: 1
---

# About the labs

Labs are the hands-on companion to the
[syllabus](/docs/training/syllabus). Each syllabus module has
one lab; this PR ships Labs M1 and M2 for Week 1. The remaining
labs (M3–M16) ship in subsequent PRs.

## The uniform six-section lab scaffold

Every lab page follows the same six sections. The
content-invariant test pins these headings so future labs that
drift fail CI.

1. **What you'll do** — two or three sentences naming the
   outcome.
2. **Before you begin** — prerequisites the learner needs
   before starting (a tenant they're an admin of, `curl`, a
   local checkout of the FastYoke repo, Node 20+, etc.).
3. **Steps** — numbered. Each step ends with a **verifiable checkpoint** — a single command the learner can run, with
   the expected output shape stated explicitly. If the step
   worked, the checkpoint says so.
4. **What you'll have at the end** — the deliverable, named
   concretely (a script file, a one-page diagram, a successful
   `curl` round-trip).
5. **Stuck?** — a one-line cross-link to the reference
   solution page.
6. **Next** — pointer to the next lab in syllabus order.

## The three-section solution scaffold

Reference solutions live on **separate pages**. The friction
of clicking through is deliberate — the lab page does not
inline answers.

1. **Reveal warning** — "This page is the answer key. Work
   the lab first; the friction of clicking through is
   intentional."
2. **Answer per step** — keyed by the lab's step numbers,
   with the actual file paths, `curl` outputs, or expected
   response shapes.
3. **Back to the lab** — cross-link back so the learner can
   resume.

## Self-paced verifiability

Same discipline as the [tutorials](/docs/tutorials): every step
ends with a single command + expected output. A learner who
worked the step can verify it at the moment, not at the very
end. If the platform's contract ever changes, the
content-invariant test catches the drift before the lab rots
silently.

## Or use Postman

Each lab from M2 onward ships with a matching Postman
request set. Import the master collection once:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

In Postman: **File → Import → Link**, paste the URL, click
**Import**. The collection's per-request **Tests** tab uses
`pm.test(...)` to enforce the same checkpoint contract the
`curl` labs pin. A green check in the Tests tab is the same
signal as a successful `curl` checkpoint.

Run each lab's folder top-to-bottom; the pre-request scripts
chain IDs between steps so you don't have to copy-paste.

The `curl` steps in each lab page remain the source of
truth. Postman is the alternate path — same labs, same
checkpoints, GUI instead of shell.

**M1 is a code-reading lab.** Postman doesn't apply there;
M2 onward all have request folders.

## What's not in labs today

- **Labs M3 through M16** — each week ships as its own PR
  using this PR's pattern as the template.
- **Capstone walkthrough** — a reference-solution example for
  the capstone project. Separate PR.
- **Per-lab sandbox environments** — learners work in their
  own tenant; no platform-side sandbox provisioning.
- **Auto-grading** — verification is by the learner running
  documented checkpoints, not by a central grader.

## See also

- [Syllabus](/docs/training/syllabus) — the 16 modules these
  labs are pinned to.
- [Capstone](/docs/training/capstone) — the final integration
  project.
- [Tutorials](/docs/tutorials) — the 101/201/301 on-ramp
  if you haven't worked it yet.
