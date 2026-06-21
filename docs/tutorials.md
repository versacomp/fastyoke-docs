---
title: Tutorials
summary: End-to-end developer build flows. Mint a token, ship something live, with verifiable checkpoints at every step. Three tiers — 101 first contact, 201 production patterns, 301 extending the platform.
order: 1
---

# Tutorials

A guided **build** flow per tutorial. Each one starts from
zero state and ends with something live in your tenant.
Every step ends with a single command you can run and an
expected output you can compare against — if the step
worked, you'll know at that moment, not at the end.

## The tier system

- **101 — First contact.** Token, one entity, one
  transition, one webhook. About **10 minutes**.
- **201 — Production patterns.** Idempotency keys, error
  recovery, scripts that survive a retry mid-batch. About
  **20 minutes**.
- **301 — Extending the platform.** Author and ship
  something other tenants could install. About **45
  minutes**.

## The verifiability discipline

Every step ends with a checkpoint the reader can run.
A successful step produces a specific, observable
result. We pin the **exact** expected output (status
code, key fields, header literal). If the platform's
response shape ever changes, the content-invariant
test fails before the tutorial rots.

## Tutorials vs. recipes

[Recipes](/docs/recipes) are different — recipes solve a single task in
isolation — copy-paste a `curl`, get the answer, move
on. Tutorials build a real end-to-end thing that
exercises multiple parts of the platform at once.

When a recipe is the right shape for what you need,
cross-link into it from your code. When you're
learning the platform, start in a tutorial.

## Tutorials vs. references

A tutorial gets you to "works on my machine" in
minutes. A reference tells you why the contract is
shaped the way it is. Tutorials cross-link reference
pages liberally — every mysterious word is a click.

## What's not in tutorials today

- **Per-Yoke tutorials** (CRM Sales, Auto Dealer, etc.)
  — those live in each Yoke's own section.
- **Tenant Yoke authoring** — graduates from 301
  extension authoring once publish-to-Marketplace
  lands.
- **Video walkthroughs** — text + verifiable command
  output only.
- **Migration / convert-from-X tutorials** — see the
  [/compare pages](/compare) instead.
- **Per-tutorial sandbox environments** — today you
  work in your own tenant.

## See also

- [101 — Your first API integration](/docs/tutorials/101-first-integration)
- [201 — Sync data into FastYoke](/docs/tutorials/201-csv-importer)
- [301 — Author an extension](/docs/tutorials/301-author-extension)
- [Recipes](/docs/recipes) — single-task snippets.
- [Developers](/docs/developers) — day-1 API invariants.
