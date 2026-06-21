---
title: 301 — Author an extension
summary: Author, sign, install, and load a tiny React extension that reads the current user's tenant_id from the SDK, fetches an entity count from the tenant API, and renders the result inside the admin shell.
order: 4
---

# 301 — Author an extension

## What you'll build

A one-route React extension that mounts inside the
admin shell. It calls `GET /entities/:slug?count=true`
from the tenant API and renders the count in a card.
End result: a new card appears for every tenant who
installs the bundle.

## Before you begin

- SDK CLI installed. See [SDK](/docs/sdk) for install
  options.
- Node 20+.
- An extension-author signing keypair. The CLI walks
  the first-time generation in step 3.

## Steps

### 1. Scaffold

```bash
fy ext init my-counter
cd my-counter
npm install
npm run dev
```

`fy ext init` produces a starter repo with a manifest,
a one-file React component, and a Vite-based dev
preview.

**Checkpoint** — the dev preview opens in a browser
and renders the starter component.

### 2. Wire one route

Open `src/CounterCard.tsx`. Replace the body with a
single call to the tenant API. The SDK provides the
`tenant_id` and a pre-baked fetch helper:

```tsx
import { useEffect, useState } from 'react';
import { useTenant, callTenantApi } from '@fastyoke/sdk';

export default function CounterCard() {
  const tenant = useTenant();
  const [count, setCount] = useState<number | null>(null);

  useEffect(() => {
    callTenantApi('/entities/customer?count=true')
      .then(r => r.json())
      .then(j => setCount(j.count));
  }, []);

  return (
    <div className="card">
      <h2>Customers in {tenant.name}</h2>
      <p>{count ?? '…'}</p>
    </div>
  );
}
```

**Checkpoint** — the preview reads the count from
your real tenant and renders the number. (The dev
server proxies the tenant API for local preview.)

### 3. Sign the bundle

```bash
fy ext sign --keypair ~/.fastyoke/ext-keys
```

`fy ext sign` builds the production bundle and emits
a `bundle.tar.gz` plus a sibling `bundle.sig`. If the
keypair doesn't exist yet, the CLI generates one and
stores it at the path you named.

**Checkpoint** — `fy ext verify ./bundle.tar.gz`
returns `OK`. Anything else means re-sign.

### 4. Install into your tenant

Open **Settings → Extensions → Upload**. Drag
`bundle.tar.gz` onto the dropzone. The platform
verifies the signature and lights the install button
on success.

After install, the new card appears wherever the
manifest declared its mount point (the starter
manifest mounts under **Workspace → Custom**).

**Checkpoint** — refresh the admin shell; the
"Customers in ..." card shows your tenant's real
count.

## What you built

A signed, verifiable extension bundle that any tenant
on the same signer roster can install. The same
shape applies to any extension you'll author —
manifest + signed bundle + admin-shell mount point.

## Cross-links

- [SDK](/docs/sdk) for the full `@fastyoke/sdk`
  surface.
- [Extensions](/docs/extensions) for the
  manifest format, signing roster, and security
  model.
- [Recipe: extension authoring](/docs/recipes/extension-authoring)
  — the recipe is the narrower task-snippet; this
  tutorial is the end-to-end ship walkthrough.

## Next

Publish-to-Marketplace authoring lands separately.
When it does, the 401-level tutorial will pick up
here and walk through the listing review process.
