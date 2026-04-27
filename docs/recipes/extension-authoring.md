# Extension Authoring

> End-to-end — scaffold, iterate locally, publish to a tenant.

# Extension Authoring

A FastYoke extension is a React bundle the admin shell loads at
runtime. The `fy` CLI handles every mechanical step; this recipe is
a full walkthrough from empty directory to installed extension.

## 1. Scaffold

```bash title="npm"
npm install -g @fastyoke/cli
fy init shift-heatmap
cd shift-heatmap
```

```bash title="pnpm"
pnpm add -g @fastyoke/cli
fy init shift-heatmap
cd shift-heatmap
```

`fy init` drops you a minimal TypeScript project:

```
shift-heatmap/
├── manifest.json
├── package.json
├── tsup.config.ts
├── src/
│   └── index.tsx
└── .gitignore
```

Install deps and you're ready:

```bash
npm install
```

## 2. Manifest shape

The scaffold's `manifest.json` is the truth the host reads on
install. Minimum required keys:

```json
{
  "id": "acme.shift-heatmap",
  "version": "0.1.0",
  "required_scopes": ["entities:read", "jobs:read"],
  "components": [],
  "pages": [
    { "name": "Shift Heatmap", "path": "/shift-heatmap" }
  ]
}
```

> **Manifest id conventions**
>
> Reverse-DNS-ish. First segment is your tenant / org; second is
>   the extension slug. The backend validator rejects slashes +
>   whitespace but doesn't enforce the dot — convention, not a
>   constraint.

## 3. Write a minimal component

Replace `src/index.tsx`:

```tsx
import React from 'react';
import { useCurrentUser, useFastYoke } from '@fastyoke/sdk';

export default function ShiftHeatmap() {
  const user = useCurrentUser();
  const { jobs } = useFastYoke();

  const [count, setCount] = React.useState<number | null>(null);
  React.useEffect(() => {
    void jobs.list({}).then((page) => setCount(page.items.length));
  }, [jobs]);

  return (
    <section style={{ padding: '2rem' }}>
      <header>
        <p style={{ color: '#6b7280', fontSize: '0.8rem' }}>
          Extension · acme.shift-heatmap
        </p>
        <h1>Welcome, {user.email}</h1>
      </header>
      <p>
        Active jobs in this tenant: {count ?? <em>loading…</em>}
      </p>
    </section>
  );
}
```

Two things are worth noting:

- `useCurrentUser()` + `useFastYoke()` come from `@fastyoke/sdk`.
  The host provides both via an import map, so your extension
  shares state (React context, SDK client) with the shell.
- We're **not** importing from `react-dom` / mounting our own root.
  The host does that — your component is a child of
  `FastYokeProvider`.

## 4. Local iteration

Spin up watch mode:

```bash
fy dev
```

esbuild rebuilds `dist/bundle.mjs` on every save. In another
terminal, keep the admin shell running (`npm run build && npm run
preview` from the main repo) — every `fy dev` rebuild reloads
seamlessly once the extension is uploaded.

> **Dev-mode caveat**
>
> <code>vite serve</code> routes the host's React through its dep
>   optimizer while extensions load via the import map — that's two
>   React instances, which breaks hooks. Always iterate against{' '}
>   <code>npm run build && npm run preview</code> on the host, or
>   against a deployed tenant.

## 5. Mint a tenant admin JWT

`fy publish` authenticates as a tenant admin. Grab a token from
the admin shell:

```bash
# Option A — browser: DevTools → Application → Local Storage →
# fastyoke-auth → copy the "token" value.
# Option B — minted via the /auth/login endpoint.
export FASTYOKE_TOKEN="eyJhbGciOi..."
```

See [Authentication](/docs/auth) for the full shape.

## 6. Build + publish

```bash
fy build
fy publish \
  --tenant https://fastyoke.example \
  --token "$FASTYOKE_TOKEN" \
  --manifest ./manifest.json \
  --bundle ./dist/bundle.mjs
```

The upload runs through the three-layer scanner (same policy as
manual file uploads). On success, you get back the
`tenant_extensions` row id — the extension is installed and
active.

## 7. Find it in the admin shell

Navigate to **Extensions** in the admin nav. Your extension shows
up with its manifest metadata and the sha256 of the bundle. The
page at `/shift-heatmap` (from your manifest's `pages[].path`)
now serves your component.

## Bumping a new version

Two rules:

1. Bump `manifest.json` → `version`. Same-version re-upload returns
   `409` to protect you from overwriting history.
2. Re-run `fy build && fy publish`.

The old version row stays in `tenant_extensions` but is flipped to
`is_active = 0`. The host serves only the new one.

## Troubleshooting

| Symptom | Fix |
|---|---|
| "bundle rejected by security scan" | Inspect what the scanner flagged — usually a third-party dep with heuristic-positive code. Swap it or vendor a trimmed copy. |
| Component renders blank | Check the browser console. Host vs extension React instance mismatch? Run the host via `preview`, not `serve`. |
| Import map not resolving | Extensions must mark `react`, `react-dom`, and `@fastyoke/sdk` as **external** in the bundler — the scaffold's `tsup.config.ts` does this; don't remove it. |

## Related

- [SDK Quickstart](/docs/sdk/quickstart) — installation + the
  host-provider pattern explained at a higher level.
- [SDK Reference](/docs/sdk/reference) — full hook + component
  surface.
