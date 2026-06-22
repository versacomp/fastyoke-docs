---
title: Lab M10 — Solution
summary: Reference solution for Lab M10 — exact REST commands per step, the retention-contract explanation, and gotchas around clone-from-source semantics and owned_schemas uniqueness.
order: 21
---

# Lab M10 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M10](/docs/training/labs/M10-marketplace). Work the
lab first — the friction of clicking through is
intentional.
::

## Answer per step

### Step 1 — List listings

```bash
curl -i -H "Authorization: Bearer $FY_PAT" \
  https://www.fastyoke.io/api/v1/tenant/marketplace/listings
```

Expected `HTTP/2 200`. JSON array; one entry has
`{"slug":"yoke-ledger", ...}`.

### Step 2 — Install

```bash
curl -i -X POST -H "Authorization: Bearer $FY_PAT" \
  https://www.fastyoke.io/api/v1/tenant/marketplace/listings/yoke-ledger/install
```

Expected `HTTP/2 200`. Side effects:
- FSM schema `gl_journal_entry` registered.
- `gl_account` entity rows seeded with the default Chart
  of Accounts (Cash, AR, AP, Revenue, COGS, Equity).
- Default invoice→AR/Revenue posting rule registered.

### Step 3 — `owned_schemas`

```bash
curl -i -H "Authorization: Bearer $FY_PAT" \
  https://www.fastyoke.io/api/v1/tenant/marketplace/listings/yoke-ledger
```

The response body includes:

```json
"owned_schemas": ["gl_journal_entry"]
```

The platform refuses (at listing registration time) any
second listing that tries to claim a schema another
listing already owns. This is the uniqueness invariant
that keeps two Yokes from fighting over the same FSM
table.

### Step 4 — Uninstall

```bash
curl -i -X POST -H "Authorization: Bearer $FY_PAT" \
  https://www.fastyoke.io/api/v1/tenant/marketplace/listings/yoke-ledger/uninstall
```

Expected `HTTP/2 200`. The listing's entitlement row is
removed; the FSM schemas and seeded `gl_account` rows
remain.

### Step 5 — Retention

```bash
curl -i -H "Authorization: Bearer $FY_PAT" \
  "https://www.fastyoke.io/api/v1/tenant/entities/gl_account?limit=10"
```

Expected `HTTP/2 200` with a non-empty array (Cash, AR,
etc.). **This is the retention contract** — every Yoke's
seeded data outlives uninstall. Reinstalling restores
the entitlement without re-seeding (it would conflict
with the existing rows otherwise).

### Step 6 — Clone

```bash
curl -i -X POST -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"slug":"my-yoke","source_slug":"yoke-ledger"}' \
  https://www.fastyoke.io/api/v1/tenant/marketplace/clone/yoke-ledger
```

Expected `HTTP/2 200` with a new Tenant Yoke metadata
object. **The clone copies the listing's *structure*, not
its *data*.** The new Yoke starts with empty entity
tables; the source's posting rules and schemas are
duplicated under your new slug.

## The retention contract — in one line

Uninstall revokes the entitlement. Uninstall **never**
deletes the data. Reinstall restores access without
re-seeding.

## Pricing models (for context)

The Marketplace ships three pricing models:

- **Free** — no charge (Yoke Ledger is this).
- **Fixed monthly** — flat subscription per the listing's
  base price.
- **Metered** — billed per usage event from the tenant's
  utility ledger.

See [/docs/marketplace](/docs/marketplace) for the
contract details.

## Back to the lab

[Lab M10 — Marketplace](/docs/training/labs/M10-marketplace).
