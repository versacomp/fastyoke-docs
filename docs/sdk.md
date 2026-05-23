---
title: SDK
summary: '@fastyoke/sdk — build extensions, page designer blocks, and host integrations.'
order: 5
---

# SDK

`@fastyoke/sdk` is the public TypeScript SDK on npm (mirrored as
`versacomp/fastyoke-sdk` for the GitHub Package Registry). Use it to
build:

- **Extensions** — React bundles that mount inside the admin shell
  and receive a scoped JWT with the current user's context.
- **Page designer blocks** — custom block types that show up in the
  Page Designer palette alongside the built-ins.
- **Host integrations** — Node or browser callers that hit the
  tenant-scoped API with a proper session token.

::section-cards{section="sdk"}
::

## Stability

The SDK follows the deployed platform version. Breaking changes are
documented with a `<Callout type="deprecated">` block on the
affected API, plus an entry in the changelog. Most changes are
additive.

## Module shape

```ts
import {
  FastYokeProvider,
  useFastYoke,
  useCurrentUser,
  ApiError,
  // ...plus block and extension registration helpers
} from '@fastyoke/sdk';
```

The SDK's types ship as TypeScript declarations in the same
package — no `@types/fastyoke-sdk` needed.

## Two environments

- **Inside the admin shell** — extensions receive the platform's
  React + SDK instances via the import map. State (React context,
  including `FastYokeContext`) crosses the boundary cleanly.
- **Outside the shell** — Node scripts or your own frontend import
  the SDK normally. You're responsible for minting a user JWT or
  using a static API key (see [Authentication](/docs/auth)).

::callout{type="warn" title="Dev-mode caveat"}
<code>vite serve</code> routes the host's React through the dep
  optimizer while extensions still resolve via the import map —
  that's two React instances, which breaks hooks. Always test
  extensions against <code>npm run build && npm run preview</code>,
  or deploy to Fly.
::
