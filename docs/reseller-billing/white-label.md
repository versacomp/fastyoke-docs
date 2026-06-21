---
title: White-label branding
summary: Configure invoice line items with your own brand. The platform never inserts the string "FastYoke" into a reseller invoice. {period} is the only built-in substitution.
order: 5
---

# White-label branding

Your billing profile controls what your end customers
see on their invoices. Configure it at
**partner-portal → Billing → Profile**, or via
`GET /profile` and `PUT /profile`.

## The brand invariant

The platform **never inserts the string "FastYoke" into a reseller invoice.** This is a load-bearing
contract enforced at the integration layer; even error
states that bypass the template never leak the string
"FastYoke" onto the customer-facing line.

If you ever see "FastYoke" appear on an end-customer
invoice, that's a platform bug — report it.

## The two profile columns

| Column | Purpose |
| --- | --- |
| `item_name_template` | Template string the platform renders per line item. Supports `{period}` substitution. |
| `item_description` | Free-form prose accompanying each line item. No substitution. |

Set both via `PUT /profile`:

```json
{
  "item_name_template": "Acme Co Platform Services — {period}",
  "item_description": "Monthly platform subscription and metered usage."
}
```

## The `{period}` substitution

`{period}` is the only built-in substitution today. At
render time the platform replaces it with the invoice's
period boundary, formatted as `YYYY-MM` (e.g., `2026-06`).

A template of `"Acme Co Platform Services — {period}"`
renders as `"Acme Co Platform Services — 2026-06"` on
the end customer's invoice line.

Other placeholders are not supported. Custom branding
beyond this template plus the description column is on
the deferred list.

## What's not in the profile today

- **Logo upload** — invoice rendering ships text-only.
- **Footer text** per invoice.
- **Multi-template** (different `item_name_template`
  per end customer).
- **Additional substitutions** beyond `{period}`.

## See also

- [Invoices](/docs/reseller-billing/invoices) — where
  the rendered line items live.
- [API reference](/docs/reseller-billing/api-reference)
  — the `/profile` endpoints.
