---
title: Skip a page when a checkbox is unchecked
summary: Use a routing rule to bypass a page based on a boolean.
order: 3
recipe: true
---

# Skip a page when a checkbox is unchecked

**When you'd use this** — opt-in subsections like "Do you want to add a
passenger?" or "Need insurance details?" — pages that are only relevant
when the submitter ticks a box on an earlier page.

## 1. Add the gating checkbox

On the page that comes *before* the optional one, drag in a **checkbox**
field:

- `key` — `add_passenger`
- Label — `Add a second passenger`

Leave it unrequired. A `checkbox` field is a boolean — `true` when
ticked, `false` (or absent) when not.

## 2. Add a routing rule on the page you want to skip

Open the optional page (say, `passenger`) and click **Routing** → **Add
rule**. In Simple mode, fill the row:

- Field — `add_passenger`
- Op — `==`
- Value — `false`
- Goto page — the page that should follow when the box is unchecked
  (e.g. `review`), or the reserved id `__end__` to short-circuit
  straight to submit.

The renderer evaluates the rule when the submitter would otherwise land
on the `passenger` page. If `add_passenger` is `false`, the rule fires
and the page is skipped; if `true`, the rule misses and the page renders
normally. Save the page — the validator rejects dangling `goto_page_id`
or unknown field refs at save time, so a typo never reaches the public
URL.

If you'd rather author the rule by hand, flip the panel to **Advanced**
mode and write `{ "==": [ { "var": "add_passenger" }, false ] }` — same
shape, same evaluator.

## See also

- [Conditional routing](/docs/forms/tutorials/301-conditional-routing)
- [Forms Builder reference](/docs/forms/builder)
