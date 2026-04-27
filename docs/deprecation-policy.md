# Deprecation Policy

> What a deprecated surface means, when it goes away, and how to migrate.

# Deprecation Policy

FastYoke is a single-binary platform under active development. We
add features often; sometimes we replace earlier ones. When that
happens, the old surface carries a **deprecation marker** until it
is actually removed — so you never find a feature gone between one
deploy and the next without warning.

## Where deprecations show up

Three signals:

- **Sidebar suffix.** A page with `deprecated: "<version>"` in its
  frontmatter renders with a "(deprecated)" suffix in the nav.
- **Top-of-page banner.** Pages whose entire subject is being
  replaced render a full-width red banner at the top, with a link
  to the replacement. Powered by the `` MDX component.
- **In-prose highlight.** When only one construct on an otherwise
  current page is deprecated, you'll see a red-bordered
  `
> **Note**
>
> ` block next to it.
>
> 
>   See the <a href="/docs/forms/builder">Forms Builder</a> page for
>   an in-prose deprecated callout on the Forms v1 schema. It's been
>   read-shimmed rather than migrated — but authors target v2.

## What the marker promises

When you see a deprecation marker, what you're reading:

- Still works today. Deprecation is an intent signal, not a kill
  switch.
- Will keep working through at least one subsequent platform
  release. We don't pull a deprecated surface in the same deploy
  that ships the replacement.
- Has a named replacement. The ``
  banner always links to the page you should migrate to. If the
  link is missing, the deprecation is scoped to information loss
  (e.g. a field being removed) — the surrounding surface stays.

## The removal contract

A deprecated surface is removed when:

1. Every shipped consumer has migrated, OR
2. The platform version notes explicitly mark the removal release.

There is no automatic sunset timer. Platform versions are releases,
not calendar dates — a deprecated surface might live for weeks or
many months depending on how load-bearing it is.

## What to do when you see the banner

1. Click through to the replacement. Read its Getting-Started-style
   setup if it has one.
2. Diff the old vs. new API shape. Most deprecations swap fields or
   rename endpoints; the migration is usually a handful of lines.
3. If the replacement is missing coverage you relied on, open an
   issue — the deprecation probably shouldn't have landed yet.

## Why no multi-version sidebar

Maintaining parallel v1 / v2 doc trees is a significant cost for a
small team, and the win is marginal when every deprecated surface
already carries a banner with the migration path. Latest-only is a
deliberate choice, not a limitation we mean to lift.

Historical docs for a removed surface live in the Git history of
this repository — the MDX source files retain the banner right up
until the removal commit that deletes them.
