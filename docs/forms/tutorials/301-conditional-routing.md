---
title: "Conditional routing"
summary: "Skip the insurance page for minors — simple mode first, then the same rule in JSONLogic."
order: 10
tier: "301"
---

# Conditional routing

::callout{type="info" title="What you'll build"}
`Patient Intake` learns to branch. Adults walk through every
page; minors skip the `insurance` page and land on `consent`.
You'll wire the rule in the builder's **Simple mode** first,
then read the generated JSONLogic in **Advanced mode** so the
two views click into place.
::

::callout{type="note" title="Prerequisites"}
Finish [Prefill fields from your entity schema](/docs/forms/tutorials/201-entity-prefill).
You need the annotated `patient` entity and a published
`Patient Intake` form with `demographics` and `contact` pages.
::

Routing rules live on a page. When the public renderer finishes
a page, it walks that page's rules top-to-bottom; the first
match wins, and the renderer jumps to the rule's
`goto_page_id` instead of the next page in document order. No
match falls through to the next page. The submit state has a
reserved id of `__end__` — a rule pointing there short-circuits
the rest of the form.

## 1. Add the `insurance` page

Earlier tutorials didn't need an `insurance` page; we'll add
one now so there's something for minors to skip.

In the Forms builder, open `Patient Intake`. In the **Pages**
rail, click **Add page** and drop it between `contact` and
`consent`. Name it `insurance`. Add two text fields:

- `insurance_provider` — label `Insurance provider`.
- `insurance_member_id` — label `Member ID`.

This page only applies to adults. Minors will skip it via the
rule you wire in step 3.

## 2. Add an `age` field to `demographics`

Open the `demographics` page and add one more field:

- `age` — type `number`, label `Age`, required, min `0`,
  max `120`.

We use a plain numeric `age` field rather than computing it
from `dob` because the JSONLogic evaluator that runs the
routing rules ships with the standard op set — no custom
`age_from_dob` op exists. Keeping the routing predicate on a
raw number makes both the simple-mode UI and the advanced-mode
JSON honest about what's being compared.

## 3. Wire the rule in Simple mode

Open the `contact` page and click **Routing**. Click **Add
rule** and fill the simple-mode row:

- Field: `age`
- Op: `<`
- Value: `18`
- Goto page: `consent`

Save the page. The branch now reads:

```text
                         (age >= 18)
┌──────────────┐    ┌─────────┐ ──────▶ ┌───────────┐    ┌─────────┐
│ demographics │ -> │ contact │         │ insurance │ -> │ consent │
└──────────────┘    └─────────┘ ──────▶ └───────────┘    └─────────┘
                         (age <  18)        ────────────▶
```

Open the admin preview. Enter `15` for `age` and fill the
contact page — clicking **Next** jumps you to `consent`,
skipping `insurance`. Reload the preview, enter `40`, and the
form steps through `insurance` as before.

::callout{type="warn" title="Save-time validation"}
The builder rejects rules with a dangling `goto_page_id`, an
unknown field reference, a cycle in the page graph, or any
page made unreachable by the new rule. Fix the rule in place
— the save endpoint returns `422` with the offending rule's
index and reason, and the panel highlights it inline.
::

## 4. Read the same rule in Advanced mode

Flip the **Routing** panel from **Simple** to **Advanced**.
The simple row compiles to a JSONLogic predicate the
evaluator can run:

```json
{ "<": [ { "var": "age" }, 18 ] }
```

The `goto_page_id` (`consent`) stays on the rule's wrapper
record; only the predicate moves into the JSON. The same
shape is what you'd hand-author for conditions Simple mode
can't express — `and` / `or` trees, `in` over an enum,
`!!` to check a checkbox is set. Anything the
`jsonlogic-rs` evaluator accepts is fair game, because the
public renderer hands the page's collected values straight
to it.

If you want minors to skip straight past `consent` too,
swap `consent` for `__end__` in the `goto_page_id` field —
the renderer short-circuits to the submit state.

## 5. Save and re-publish

Click **Save**, then **Publish**. The published version row
freezes the routing graph the same way it freezes fields, so
public submitters get the branch you just tested. Edits to
the rule after this point need another **Publish** to reach
the public URL.

## Verify it worked

Open the admin preview for `Patient Intake`:

- Enter `age = 15`. Walk forward from `contact`. The
  `insurance` page is skipped; you land on `consent`.
- Reload, enter `age = 40`. The form steps through
  `insurance` before `consent`, exactly as document order
  suggests.

If both runs match, the rule is live.

## Next

Continue with [File uploads and scanning](/docs/forms/tutorials/301-file-uploads-and-scanning).
