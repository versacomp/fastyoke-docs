---
title: SDK Quickstart
summary: Install @fastyoke/sdk and mount your first extension.
order: 1
---

# SDK Quickstart

::callout{type="tip" title="Building a standalone app instead?"}
This page covers SDK use inside admin-shell extensions. If you want a
standalone Vite + React SPA signed in with operator credentials and
deployed to Cloudflare Pages, follow
[SPA on Cloudflare Pages with the React SDK](/docs/recipes/spa-on-cloudflare-pages)
instead.
::

## Install

::code-group
```bash title="npm"
npm install @fastyoke/sdk
```

```bash title="pnpm"
pnpm add @fastyoke/sdk
```

```bash title="yarn"
yarn add @fastyoke/sdk
```
::

React is a peer dep — if your project doesn't already have React 18,
install it too.

## Your first extension

Extensions are React bundles the admin shell loads at runtime. The
minimal shape is a default-exported component:

```tsx title="src/MyExtension.tsx"
import { useFastYoke } from '@fastyoke/sdk';

export default function MyExtension() {
  const { tenantId, projectId } = useFastYoke();
  return (
    <section>
      <h2>Hello from your extension</h2>
      <p>Tenant: {tenantId}</p>
      {projectId && <p>Project: {projectId}</p>}
    </section>
  );
}
```

`useFastYoke()` reads from the `FastYokeContext` the host
provides. When the extension is mounted, it's automatically wrapped
in a provider carrying the active tenant/project plus typed clients
(`schemas`, `jobs`, `entities`, `pages`, `files`, `extensions`).
The host's signed-in identity is on the JWT those clients carry —
read it server-side from `/api/v1/auth/me` if you need user
fields beyond `tenantId`.

## The manifest

Every extension ships a `manifest.json` alongside the bundle. Minimum
required keys:

```json title="manifest.json"
{
  "id": "acme.shift-heatmap",
  "name": "Shift Heatmap",
  "entry": "dist/index.mjs",
  "required_scopes": ["entities:read", "jobs:read"]
}
```

::callout{type="info" title="Scope enforcement"}
Scopes are enforced at runtime. Every API call carrying an
  extension-scoped JWT or API token is checked against
  <code>required_scopes</code>; a missing scope rejects the request
  with HTTP 403. Declare every scope your code calls into.
::

## Bundling

The host provides React, `react-dom`, and `@fastyoke/sdk` via an
import map. Mark those as external when you bundle so the browser
resolves them to the host's instances. `fy build` and `fy dev` do
this out of the box — pass `--entry` / `--outfile` to override
defaults:

```bash
# One-shot build with the right externals already configured.
fy build

# Watch mode against the same config, for live iteration.
fy dev
```

If you bundle by hand instead, the externals you must preserve are
`react`, `react-dom`, `react/jsx-runtime`, and `@fastyoke/sdk`.

## Upload + install

From the admin shell:

1. Navigate to **Extensions**.
2. Click **Upload** and pick a zip with your bundle + manifest.
3. The three-layer scanner runs the same pipeline as form
   attachments — MIME magic, VirusTotal hash lookup, LLM content
   inspection.
4. If it passes, **Install** to enable for your tenant.
5. Refresh. Your extension shows up in the nav under the category
   its manifest declared.

See [Extensions](/docs/extensions) for the consumer-side admin
walkthrough.
