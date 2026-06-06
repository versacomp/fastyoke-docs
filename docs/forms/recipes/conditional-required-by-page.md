---
title: Make a field effectively required only on a later page
summary: Keep a field optional early, then use a routing rule to block navigation past a later page until it's filled.
order: 4
recipe: true
---

# Make a field effectively required only on a later page

**When you'd use this** — a field that's offered as optional during
intake but has to be present by the time the submitter reaches a later
gate (e.g. `delivery_notes` optional on `cart`, mandatory by
`checkout`).

## 1. Leave the field unrequired on the early page

On the intake page, drag the field in as usual but leave **Required**
off. Submitters can advance without filling it, which is the whole point
— most won't, and you don't want to make them.

## 2. Block navigation past the gate page with a routing rule

The builder's per-field `required` flag is a single boolean — it doesn't
vary by page. To get per-page behavior, lean on routing instead. Open
the page that should *enforce* the value (say, `checkout`), click
**Routing** → **Add rule**, and in Simple mode fill the row:

- Field — `delivery_notes`
- Op — `==`
- Value — `""` (empty string)
- Goto page — the same gate page (e.g. `checkout`)

The rule fires when the field is still empty at the moment the renderer
would advance off `checkout`, sending the submitter right back to it.
Pair the rule with a `static` text block on the page that explains *why*
they're stuck — otherwise the loop reads as a bug. Once the field has a
value, the rule misses and the form advances normally.

If you'd rather use Advanced mode, the same predicate is
`{ "==": [ { "var": "delivery_notes" }, "" ] }`. The save-time
validator rejects rules that would make any page unreachable, so a
typo'd field key surfaces immediately.

## See also

- [Conditional routing](/docs/forms/tutorials/301-conditional-routing)
- [Forms Builder reference](/docs/forms/builder)
