---
title: Lecture M10 — Marketplace
summary: The install / inspect / uninstall / clone loop, the retention contract on uninstall, and the difference between a Marketplace app and a Tenant Yoke.
order: 11
---

# Lecture M10 — Marketplace

## The mental model

The Marketplace is the distribution channel for **Yokes**
— FastYoke-vetted apps that install into a tenant. Each
listing carries its entity schemas, FSMs, pages, and
seeded rows. Installing is a transaction; uninstalling
removes the listing but preserves the data it seeded.

## Key concepts

- **Apps + UI pages + FSM schemas + seeded rows.** Every
  install is the four together. The seed rows show up
  immediately so the app is useful on day one.
- **Marketplace install.** Recorded in
  `marketplace_installs`, scoped to the tenant. Gates
  whether the app's routes are reachable.
- **Retention contract.** Uninstalling deletes the
  listing only. Data the app seeded stays. This survives
  misclicks and operator turnover.
- **Tenant Yokes.** A tenant can clone a Marketplace
  listing into a **Tenant Yoke** — their own customizable
  copy. Customizing a Tenant Yoke is the on-ramp to
  publishing your own marketplace listing eventually.
- **Owned-schemas separation.** A clone copies the
  schemas; the originals remain owned by the source
  Yoke. Updating the source doesn't migrate the clone —
  that's a feature, not a bug.

## Common pitfalls

- **Expecting uninstall to be a "factory reset."** It
  isn't — the retention contract is deliberate. To
  actually purge data, delete the rows explicitly first.
- **Treating a clone like a fork that auto-merges
  upstream.** It doesn't. Once cloned, the Tenant Yoke
  is yours; upstream updates don't reach it.
- **Skipping marketplace gating in extension code.**
  Forgetting the entitlement check is how you ship a
  paid feature for free.

## Where to go next

- Lab: [M10 — Marketplace](/docs/training/labs/M10-marketplace).
- Reference: [/docs/marketplace](/docs/marketplace).
