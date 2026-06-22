---
title: Quiz M7 — Pages & App Builder
summary: 10 multiple-choice questions on Pages vs Apps, the block catalog, theme inheritance, and the Advanced App Builder.
order: 8
---

# Quiz M7 — Pages & App Builder

Pairs with [Lecture M7](/docs/training/lectures/M7-pages-apps)
and [Lab M7](/docs/training/labs/M7-pages-and-apps).

**Q1.** Difference between a Page and an App?

a) Apps are Pro+
b) Page = single route; App = bundle of pages with nav + theme + route prefix
c) Pages are static; Apps are dynamic
d) They're aliases

<details>
<summary>Answer + why</summary>

**b)** Apps install from the marketplace; pages compose
the App.

</details>

**Q2.** What does the Advanced App Builder add over the standard one?

a) A separate runtime
b) LLM-assisted authoring that produces the same block JSON
c) Custom HTML
d) Server-side rendering

<details>
<summary>Answer + why</summary>

**b)** The LLM is a UX layer over the same block catalog.

</details>

**Q3.** How does theme inheritance work across nested pages?

a) Each page is independent
b) Theme is set per App and inherits across the App's pages
c) Theme is per tenant only
d) Theme is per route

<details>
<summary>Answer + why</summary>

**b)** App-scoped — so installing two apps doesn't make
each override the other.

</details>

**Q4.** What's the right way to embed arbitrary HTML?

a) Use the `Html` block
b) There isn't one — write an extension (M9) if needed
c) PATCH the page JSON
d) Drop into a `Markdown` block

<details>
<summary>Answer + why</summary>

**b)** Composition stays in the typed catalog. Custom HTML
goes through extensions for safety.

</details>

**Q5.** Block configs are…

a) Code
b) JSON validated against per-block schemas
c) YAML
d) Free-form

<details>
<summary>Answer + why</summary>

**b)** Each block declares a schema; the runtime
validates configs at render time.

</details>

**Q6.** The list-page renderer and `EntityDetailRenderer` use…

a) The same block vocabulary
b) Different block vocabularies — the detail renderer's is richer
c) The same blocks but different layouts
d) A shared vocabulary that's expanded for detail

<details>
<summary>Answer + why</summary>

**b)** Two vocabularies. Check the detail one before
assuming you need a new block.

</details>

**Q7.** What's an App's route prefix?

a) Configurable per-App slug under the admin shell
b) Always `/app/`
c) The App name
d) Configured at install

<details>
<summary>Answer + why</summary>

**a)** Each App lives at its own slug — multiple Apps
co-exist without route collisions.

</details>

**Q8.** What happens if a block's config doesn't validate?

a) Renders silently broken
b) Page rejects render with a structured error
c) Falls back to defaults
d) Returns 500

<details>
<summary>Answer + why</summary>

**b)** Surfaces the bug early; doesn't silently hide it.

</details>

**Q9.** Themed at the tenant level by accident?

a) Affects only the App you intended to theme
b) Bleeds across every App in that tenant
c) Reset on next deploy
d) Refused at save time

<details>
<summary>Answer + why</summary>

**b)** Tenant-level overrides apply globally — usually
not what you want. Theme inside the App.

</details>

**Q10.** Should you reach for a new custom block before exhausting the catalog?

a) Yes — extensibility first
b) No — most "new blocks" exist in the detail renderer
c) Always start with custom; the catalog is legacy
d) Only on Enterprise

<details>
<summary>Answer + why</summary>

**b)** Read the marketplace apps guide before authoring.
Many "new blocks" already exist.

</details>
