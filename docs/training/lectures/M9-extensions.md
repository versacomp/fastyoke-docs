---
title: Lecture M9 — Extensions
summary: Why extensions are signed WASM modules with no host syscalls, what the manifest scope vocabulary controls, and the dev-loop tradeoffs.
order: 10
---

# Lecture M9 — Extensions

## The mental model

When the block catalog can't express what you need,
**extensions** let you ship custom logic — a guard
function, a side effect on transition, a custom block.
Extensions are WASM modules. They run inside a `wasmtime`
sandbox with fuel + memory caps and no host syscalls
beyond the documented WIT host imports.

## Key concepts

- **Signed bundles.** Extensions are signed before install.
  The platform refuses to load an unsigned or
  wrong-signed bundle. Authoring keys are tenant-scoped.
- **Manifest scopes.** The manifest declares which
  permissions the extension wants — drawn from the same
  72-permission catalog as PATs. Install fails if the
  manifest asks for more than the installing admin can
  grant.
- **WIT host imports.** The only way the sandbox talks to
  the host. Storage reads, entity reads/writes,
  outbound HTTP through the SSRF-guarded fetcher — never
  raw I/O.
- **Fuel + memory caps.** Every invocation has a fuel
  budget and a memory ceiling. Exceeding either kills the
  call cleanly.
- **Dev loop.** The CLI scaffolds, builds, signs, and
  installs locally. The cloud sandbox is the
  authoritative environment — never assume your local
  shape generalizes.

## Common pitfalls

- **Asking for "just file I/O."** There isn't any. Use
  the WIT host imports or rethink whether you actually
  need persistence outside an entity payload.
- **Writing extensions in JS first.** The SDK supports
  it via the QuickJS-on-WASM bridge, but Rust extensions
  are smaller, faster, and easier to debug.
- **Skipping signing in dev.** You can't — the platform
  refuses unsigned bundles. The CLI signs with your dev
  key automatically.

## Where to go next

- Lab: [M9 — Extensions](/docs/training/labs/M9-extensions).
- Reference: [/docs/extensions](/docs/extensions).
