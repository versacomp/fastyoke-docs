---
title: Lab M10 — Marketplace
summary: Install Yoke Ledger via REST, inspect what it seeds (owned_schemas + default Chart of Accounts), uninstall, verify the retention contract (rows survive), then author a Tenant Yoke via clone-from-source.
order: 20
---

# Lab M10 — Marketplace

This lab pairs with **Module M10 — Marketplace & Tenant
Yokes** in the [syllabus](/docs/training/syllabus).

## What you'll do

Install the Yoke Ledger Marketplace listing via the REST
endpoint. Inspect what it seeds (FSM schemas + default
Chart of Accounts). Uninstall. Confirm the seeded data is
**retained** — that's the marketplace retention contract.
Then author a Tenant Yoke by cloning from a seed.

## Before you begin

- A FastYoke tenant you're an admin of.
- A `fy_pat_` PAT with `marketplace:admin` (or any scope
  that lets you call the marketplace endpoints — admin
  session works too).
- `curl`.
- About 30 minutes.

## Or use Postman

If you prefer a GUI, run the lab steps from Postman
instead of `curl`. Each request has a **Tests** tab with
the same checkpoint assertions the `curl` lab pins.

**Import once:** in Postman, choose **File → Import →
Link** and paste this URL:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

Click **Import**. Open the **Week 5 — Extending the
platform → M10 — Marketplace** folder, set the
`FY_TOKEN` environment variable, then run each request in
order. Step 4 asserts the seeded `gl_account` rows
survive uninstall — that's the retention-contract check.

The `curl` steps below remain the source of truth.

## Steps

1. **List available listings.**

   ```bash
   curl -i -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/marketplace/listings
   ```

   **Checkpoint:** 200; JSON array contains an entry with
   `slug: "yoke-ledger"`.
2. **Install Yoke Ledger.**

   ```bash
   curl -i -X POST -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/marketplace/listings/yoke-ledger/install
   ```

   **Checkpoint:** 200. A subsequent
   `GET /entities/gl_account` returns the seeded default
   Chart of Accounts (Cash, AR, AP, Revenue, etc.).
3. **Inspect `owned_schemas`.** Pull the listing's
   metadata:

   ```bash
   curl -i -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/marketplace/listings/yoke-ledger
   ```

   **Checkpoint:** the response names `owned_schemas`
   including `gl_journal_entry`. These are the FSM
   schemas the listing seeded; the platform enforces that
   no other listing can own them.
4. **Uninstall.**

   ```bash
   curl -i -X POST -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/marketplace/listings/yoke-ledger/uninstall
   ```

   **Checkpoint:** 200. The listing is gone from the
   active list.
5. **Verify retention.**

   ```bash
   curl -i -H "Authorization: Bearer $FY_PAT" \
     "https://www.fastyoke.io/api/v1/tenant/entities/gl_account?limit=10"
   ```

   **Checkpoint:** 200 and the seeded `gl_account` rows
   are **still there**. This is the **retention contract**
   — uninstall removes the listing but never deletes the
   data it seeded. Your books survive a misclick.
6. **Author a Tenant Yoke by clone.**

   ```bash
   curl -i -X POST -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"slug":"my-yoke","source_slug":"yoke-ledger"}' \
     https://www.fastyoke.io/api/v1/tenant/marketplace/clone/yoke-ledger
   ```

   **Checkpoint:** 200 with the new Tenant Yoke metadata.

## What you'll have at the end

A demonstrated install → inspect → uninstall →
retention-survived loop, plus a Tenant Yoke authored by
cloning the Yoke Ledger seed.

## Stuck?

See [Lab M10 — Solution](/docs/training/labs/M10-marketplace-solution).

## Next

[Lab M11 — Webhooks](/docs/training/labs/M11-webhooks).
