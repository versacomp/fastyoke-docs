---
title: Lecture M7 — Pages & App Builder
summary: What separates a Page from an App, the block-based composition model, and where the Advanced Builder adds LLM assistance.
order: 8
---

# Lecture M7 — Pages & App Builder

## The mental model

The admin shell renders **Apps** — collections of **Pages**
— composed from a catalog of typed **blocks**. Each block
declares its inputs (the entity it lists, the schema it
forms against). The composition is data, not code; the
runtime takes the JSON and renders it.

## Key concepts

- **Page.** A single rendered route — list view, detail
  view, form, dashboard. Composed of blocks.
- **App.** A bundle of pages with shared navigation, theme,
  and route prefix. Apps are what installs from the
  marketplace.
- **Block catalog.** A typed catalog: `EntityList`,
  `EntityDetail`, `Form`, `Chart`, etc. Each block has a
  JSON schema for its config.
- **Theme & CSS.** Apps can override the default theme.
  Overrides are scoped to the app, not the tenant.
- **Advanced App Builder.** LLM-assisted; produces the
  same block-JSON the standard builder produces. The LLM
  is a UX layer, not a separate runtime.
- **Two block vocabularies.** The list-page renderer and
  the `EntityDetailRenderer` use different block
  vocabularies. The detail renderer's vocabulary is
  richer; check it before assuming a "new block" is needed.

## Common pitfalls

- **Trying to embed arbitrary HTML in a page.** There's no
  generic `Html` block. Compose with what's in the catalog,
  or write an extension (M9) if you need to.
- **Theming at the tenant level by accident.** Overrides
  go in the App, not the tenant defaults — otherwise
  every app a tenant installs inherits the override.
- **Inventing a block.** Read [the marketplace apps
  guide](https://github.com/anthropics) — many "new
  blocks" are already in the detail renderer.

## Where to go next

- Lab: [M7 — Pages & App Builder](/docs/training/labs/M7-pages-and-apps).
- Reference: [/docs/pages](/docs/pages), [/docs/apps](/docs/apps), [/docs/apps/advanced](/docs/apps/advanced).
