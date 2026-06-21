---
title: API reference
summary: REST surface for Yoke Ledger — accounts, journal entries (with post and reverse), posting rules, the sources catalogue, and the three financial statements.
order: 6
---

# API reference

Every endpoint is tenant-scoped. The platform derives the
`tenant_id` from the JWT; you never pass it in the body or
in a path segment.

## Auth and gating

- **Auth:** tenant JWT in `Authorization: Bearer …`.
- **Gating:** the tenant must hold the
  `fastyoke.yoke-ledger` entitlement. Install auto-grants
  it for free; immediately after uninstall a request can
  return `402 not_entitled` with the listing slug in the
  body.

## Accounts

| Method | Path | Purpose |
| --- | --- | --- |
| GET   | `/api/v1/tenant/gl/accounts` | List the Chart of Accounts. |
| POST  | `/api/v1/tenant/gl/accounts` | Create an account (code + name + type). |
| PATCH | `/api/v1/tenant/gl/accounts/:code` | Rename, recategorize, or deactivate. |

Wrong-case account types (`asset` instead of `Asset`) and
duplicate codes are validation errors on create and patch.

## Entries

| Method | Path | Purpose |
| --- | --- | --- |
| GET  | `/api/v1/tenant/gl/entries` | List entries. Filterable by state and date range. |
| POST | `/api/v1/tenant/gl/entries` | Create a draft entry with one or more lines. |
| GET  | `/api/v1/tenant/gl/entries/:id` | Read one entry with its lines. |
| POST | `/api/v1/tenant/gl/entries/:id/post` | Transition draft → posted. |
| POST | `/api/v1/tenant/gl/entries/:id/reverse` | Write a mirror entry that reverses a posted one. |

**Validation:** `POST /entries` and `POST /entries/:id/post`
return `unbalanced` when the lines don't sum to a balanced
entry. `POST /entries/:id/post` and `POST /entries/:id/reverse`
return `immutable` when called against an entry in a state
that doesn't allow the transition.

## Posting rules

| Method | Path | Purpose |
| --- | --- | --- |
| GET  | `/api/v1/tenant/gl/posting-rules` | List your tenant's rules. |
| POST | `/api/v1/tenant/gl/posting-rules` | Create a rule. |
| GET  | `/api/v1/tenant/gl/posting-rules/sources` | Catalogue of source kinds available to your tenant. |

## Reports

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/api/v1/tenant/reports/trial-balance` | Trial Balance over an optional date range. |
| GET | `/api/v1/tenant/reports/pnl` | Profit & Loss over an optional period. |
| GET | `/api/v1/tenant/reports/balance-sheet` | Balance Sheet as of an optional date. |

## Error codes you'll see

| Code | Meaning | Returned by |
| --- | --- | --- |
| `unbalanced` | Lines don't sum to a balanced entry. | `POST /entries`, `POST /entries/:id/post` |
| `not_entitled` | Tenant doesn't hold `fastyoke.yoke-ledger`. | Any endpoint, immediately after uninstall. |
| `immutable` | Tried to mutate a posted entry. | Edit / delete of a posted entry, double-post, post-after-reverse. |

## See also

- [Overview](/docs/yoke-ledger) — what the Yoke is and
  who installs it.
- [Journal entries](/docs/yoke-ledger/journal-entries) —
  the lifecycle these endpoints drive.
- [Posting rules](/docs/yoke-ledger/posting-rules) — the
  consumer of the `/sources` catalogue.
