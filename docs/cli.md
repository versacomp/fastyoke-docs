---
title: CLI
summary: 'fy — the extension-authoring CLI.'
order: 6
---

# CLI

FastYoke ships a single public command-line surface:

- **`@fastyoke/cli`** (binary name **`fy`**) — public npm package.
  Covers three authoring workflows: a single-extension scaffold
  (`fy init`, with **admin extension** and **standalone Next.js
  consumer app** template variants), a full-app scaffold
  (`fy app create` / `fy app add-entity`) that emits a typed CRUD
  app from an entity + FSM spec, and a machine-readable discovery
  manifest (`fy capabilities`) for LLM agents and tooling.
  Rebuilds bundles in watch mode and uploads the finished
  artifact to a running tenant. Not a general-purpose API client;
  it only does what the scaffolding + extension-authoring
  workflows need.

::section-cards{section="cli"}
::

## Which one do you want

| I want to... | Use |
|---|---|
| Scaffold a new extension project | `fy init` |
| Scaffold a standalone Next.js consumer app | `fy init --template nextjs` |
| Scaffold a standalone Vite SPA (operator portal, CF Pages) | `fy init --template vite-spa` |
| Scaffold a full app extension from an entity + FSM spec | `fy app create` |
| Add another entity to an existing app project | `fy app add-entity <Name>` |
| Rebuild an extension in watch mode | `fy dev` |
| Upload an extension bundle to a tenant | `fy publish` |
| Discover CLI commands + flags as JSON (for LLM agents) | `fy capabilities` |

## What the CLI **doesn't** do

A FastYoke-as-API SDK/CLI surface (`schemas list`, `jobs transition`,
`entities get`, etc.) does not exist today. For that kind of
automation you call the tenant-scoped HTTP API directly with a
tenant JWT minted from `POST /api/v1/auth/login`. See
[Authentication](/docs/auth) for the full token taxonomy and the
[CI scripting recipe](/docs/recipes/ci-scripting) for reusable
curl helpers.
