# CLI

> fy for extension authors + fastyoke-admin for operators.

# CLI

FastYoke ships two command-line surfaces — one each for the two
distinct non-browser audiences:

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
- **`fastyoke-admin`** — the operator CLI baked into the Docker
  image at `/app/fastyoke-admin`. Platform-level ops (super-admin
  lifecycle, Litestream backup hooks, tenant reassign/restore,
  the cutover sequence). Not distributed via npm; only available
  inside a running container.

> **Web-UI parity**
>
> Everything <code>fastyoke-admin</code> does for super-admin
>   lifecycle, tenant reassign/restore, and the cutover sequence is
>   also available from the platform-admin shell
>   (<code>/super/admins</code>, per-tenant Danger Zone,{' '}
>   <code>/super/maintenance</code>). The CLI remains the path for
>   automation and for post-volume-loss recovery when the app is
>   down; the shell is the convenient interactive surface.

## Which one do you want

| I want to... | Use |
|---|---|
| Scaffold a new extension project | `fy init` |
| Scaffold a standalone Next.js consumer app | `fy init --template nextjs` |
| Scaffold a full app extension from an entity + FSM spec | `fy app create` |
| Add another entity to an existing app project | `fy app add-entity ` |
| Rebuild an extension in watch mode | `fy dev` |
| Upload an extension bundle to a tenant | `fy publish` |
| Discover CLI commands + flags as JSON (for LLM agents) | `fy capabilities` |
| Create the first super-admin on deploy | `fastyoke-admin create-admin` |
| Restore a tenant's SQLite file from backup | `fastyoke-admin restore-tenant` (or `/super/tenants/:id`) |
| Move a tenant to a different Fly machine | `fastyoke-admin reassign-tenant` (or `/super/tenants/:id`) |
| Check cutover readiness | `fastyoke-admin mirror-cleanup-status` (or `/super/maintenance`) |

## What the CLIs **don't** do

A FastYoke-as-API SDK/CLI surface (`schemas list`, `jobs transition`,
`entities get`, etc.) does not exist today. For that kind of
automation you call the tenant-scoped HTTP API directly with a
tenant JWT minted from `POST /api/v1/auth/login`. See
[Authentication](/docs/auth) for the full token taxonomy and the
[CI scripting recipe](/docs/recipes/ci-scripting) for reusable
curl helpers.
