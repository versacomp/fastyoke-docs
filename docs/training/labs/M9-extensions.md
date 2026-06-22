---
title: Lab M9 — Extensions
summary: Scaffold a starter extension with fy ext init, wire one SDK route fetching the vehicle entity count, sign with fy ext sign, verify with fy ext verify, install via the admin shell, observe the card render in the tenant.
order: 18
---

# Lab M9 — Extensions

This lab pairs with **Module M9 — Extensions** in the
[syllabus](/docs/training/syllabus).

## What you'll do

Scaffold a starter extension with the SDK CLI. Wire one
route that uses the SDK to fetch an entity count from the
tenant API. Sign the bundle. Verify the signature against
the platform's signing roster. Install into your tenant.
Observe the card render in the admin shell.

## Before you begin

- SDK CLI installed. See [/docs/cli](/docs/cli) for install
  options.
- Node 20+.
- A FastYoke tenant you're an admin of.
- The `vehicle` entity from M3 (any entity works; this lab
  uses `vehicle` as the demo target).
- An extension-author signing keypair. The CLI generates
  one on first `fy ext sign` if you don't have one.
- About 45 minutes.

## Or use Postman

The extension JWT is minted by the platform at extension
load time — there's no developer-side mint endpoint to
call. Postman doesn't apply here. Run the CLI commands
below and install through the admin shell.

## Steps

1. **Scaffold the starter project.**

   ```bash
   fy ext init my-counter
   cd my-counter
   npm install
   ```

   **Checkpoint:** the project builds and `npm run dev`
   opens the local preview without errors.
2. **Read the manifest.** Open `manifest.json`. Identify
   the `required_scopes` field.

   **Checkpoint:** name the scopes the starter manifest
   requests. (Default: `data:read`.)
3. **Wire one route to fetch entity count.** Edit
   `src/CounterCard.tsx`. Use the SDK to call the entity
   API:

   ```tsx
   import { useEffect, useState } from 'react';
   import { useTenant, callTenantApi } from '@fastyoke/sdk';

   export default function CounterCard() {
     const tenant = useTenant();
     const [count, setCount] = useState<number | null>(null);
     useEffect(() => {
       callTenantApi('/entities/vehicle?count=true')
         .then(r => r.json())
         .then(j => setCount(j.count));
     }, []);
     return (
       <div className="card">
         <h2>Vehicles in {tenant.name}</h2>
         <p>{count ?? '…'}</p>
       </div>
     );
   }
   ```

   **Checkpoint:** `npm run dev` shows the count rendering
   in the local preview (the SDK dev server proxies the
   tenant API).
4. **Sign the bundle.**

   ```bash
   fy ext sign --keypair ~/.fastyoke/ext-keys
   ```

   **Checkpoint:** `bundle.tar.gz` and `bundle.sig` exist
   side by side in the project root.
5. **Verify the signature.**

   ```bash
   fy ext verify ./bundle.tar.gz
   ```

   **Checkpoint:** the verify command prints `OK`. If it
   fails, re-sign — the platform's signing roster won't
   accept a bundle whose signature can't be verified
   locally.
6. **Install into your tenant.** Open Settings →
   Extensions → Upload. Drag `bundle.tar.gz` onto the
   dropzone.

   **Checkpoint:** the platform verifies the signature
   against the platform roster and lights the install
   button. Click it.
7. **Observe the card render.** Refresh the admin shell.
   Navigate to the page the starter manifest declared as
   the mount point.

   **Checkpoint:** the "Vehicles in {tenant name}" card
   appears with a number — your real tenant's vehicle
   count. Note the extension JWT is short-lived (15
   minutes) and auto-refreshed by the platform; you
   don't manage that timer.

## What you'll have at the end

A signed extension installed in your tenant rendering a
real entity count.

## Stuck?

See [Lab M9 — Solution](/docs/training/labs/M9-extensions-solution).

## Next

[Lab M10 — Marketplace](/docs/training/labs/M10-marketplace).
