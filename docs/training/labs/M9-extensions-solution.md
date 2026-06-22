---
title: Lab M9 — Solution
summary: Reference solution for Lab M9 — exact CLI output, manifest walkthrough, SDK call shape, and four common gotchas around signing, JWT TTL, and hard refusals.
order: 19
---

# Lab M9 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M9](/docs/training/labs/M9-extensions). Work the lab
first — the friction of clicking through is intentional.
::

## Answer per step

### Step 1 — Scaffold output

`fy ext init my-counter` creates a Vite + React project
with the following shape:

```
my-counter/
  manifest.json
  package.json
  src/
    CounterCard.tsx     # the starter route
    main.tsx
  index.html
```

The starter `package.json` includes `dev` and `build`
scripts. `npm run dev` boots a local preview at
`http://localhost:5173`.

### Step 2 — Manifest fields

`manifest.json` carries:

- `name` — display name in the admin shell.
- `version` — semver string.
- `required_scopes` — array. The starter requests
  `["data:read"]`. The platform mints a 15-minute
  extension JWT carrying exactly these scopes when the
  extension loads.
- `mount` — where in the admin shell the extension's
  primary route renders.

### Step 3 — The SDK call

`@fastyoke/sdk`'s `callTenantApi(path)` wraps fetch with
the extension JWT already attached. The dev server
proxies tenant-API calls during `npm run dev` so the
local preview works without a tunnel.

### Step 4 — Sign output

`fy ext sign --keypair ~/.fastyoke/ext-keys` produces:

```
bundle.tar.gz   # gzipped tarball of the built extension
bundle.sig      # ed25519 detached signature
```

If `~/.fastyoke/ext-keys` doesn't exist, the CLI
generates a new keypair there. Back the keypair up — the
platform's signing roster ties your identity to the
public key, and losing the private key means re-issuing
the public key on the roster.

### Step 5 — Verify output

`fy ext verify ./bundle.tar.gz` reads the signature and
checks it against the keypair's public key. Output:

```
OK
```

Verify failure usually means the keypair isn't on the
platform's signing roster. The operator-side process to
add a roster entry is internal — not something the
developer runs.

### Step 6 — Install

The admin shell's upload dropzone parses the bundle,
fetches the signature, and re-verifies it server-side
against the roster. A roster-mismatched bundle is
refused with a clear error and never installs.

### Step 7 — Card render

The card renders the tenant's vehicle count. The
extension JWT carries the `data:read` scope from the
manifest; if you added a write call without updating
`required_scopes`, the platform would 403 the call.

## Common gotchas

- **Signing keypair generation on first sign.** The
  keypair lands at the path you passed; back it up. Losing
  it means re-issuing your roster entry.
- **Extension JWT TTL is 15 minutes.** The platform
  re-mints automatically on the next request after
  expiry; the developer doesn't manage this timer.
- **Required scopes are a contract.** Adding an API call
  that needs a scope your manifest didn't request → 403.
  Update the manifest, re-sign, re-install.
- **The four hard refusals.** An extension cannot install
  another extension, mint API tokens, revoke API tokens,
  or uninstall extensions. These refuse at the platform
  layer regardless of scopes.

## Back to the lab

[Lab M9 — Extensions](/docs/training/labs/M9-extensions).
