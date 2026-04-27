# SDK Quickstart

> Install @fastyoke/sdk and mount your first extension.

# SDK Quickstart

## Install

```bash title="npm"
npm install @fastyoke/sdk
```

```bash title="pnpm"
pnpm add @fastyoke/sdk
```

```bash title="yarn"
yarn add @fastyoke/sdk
```

React is a peer dep — if your project doesn't already have React 18,
install it too.

## Your first extension

Extensions are React bundles the admin shell loads at runtime. The
minimal shape is a default-exported component:

```tsx title="src/MyExtension.tsx"
import { useCurrentUser } from '@fastyoke/sdk';

export default function MyExtension() {
  const user = useCurrentUser();
  return (
    <section>
      <h2>Hello, {user.email}</h2>
      <p>Tenant: {user.tenant_id}</p>
    </section>
  );
}
```

`useCurrentUser()` reads from the `FastYokeContext` the host
provides. When the extension is mounted, it's automatically wrapped
in a provider carrying the signed-in user's context.

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

> **Scope enforcement**
>
> Scopes are advisory today — they're stamped into the JWT and
>   validated in a future phase. Declare them truthfully now so the
>   flip-the-switch migration is a no-op for your code.

## Bundling

The host provides React, `react-dom`, and `@fastyoke/sdk` via an
import map. Mark those as external when you bundle so the browser
resolves them to the host's instances:

```ts title="tsup.config.ts"
import { defineConfig } from 'tsup';

export default defineConfig({
  entry: ['src/index.tsx'],
  format: ['esm'],
  external: ['react', 'react-dom', 'react/jsx-runtime', '@fastyoke/sdk'],
  dts: true,
});
```

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
