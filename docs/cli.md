# CLI

> fy for extension authors + fastyoke-admin for operators.

# CLI

FastYoke ships two command-line surfaces — one each for the two
distinct non-browser audiences:

- **`@fastyoke/cli`** (binary name **`fy`**) — public npm package
  for **extension authors**. Scaffolds a project from a template,
  rebuilds the bundle in watch mode, and uploads the finished
  artifact to a running tenant. Not a general-purpose API client;
  it only does what the extension-authoring workflow needs.
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
| Rebuild an extension in watch mode | `fy dev` |
| Upload an extension bundle to a tenant | `fy publish` |
| Create the first super-admin on deploy | `fastyoke-admin create-admin` |
| Restore a tenant's SQLite file from backup | `fastyoke-admin restore-tenant` (or `/super/tenants/:id`) |
| Move a tenant to a different Fly machine | `fastyoke-admin reassign-tenant` (or `/super/tenants/:id`) |
| Check cutover readiness | `fastyoke-admin phase13-status` (or `/super/maintenance`) |

## What the CLIs **don't** do

A FastYoke-as-API SDK/CLI surface (`schemas list`, `jobs transition`,
`entities get`, etc.) does not exist today. For that kind of
automation you call the tenant-scoped HTTP API directly with a
tenant JWT minted from `POST /api/v1/auth/login`. See
[Authentication](/docs/auth) for the full token taxonomy and the
[CI scripting recipe](/docs/recipes/ci-scripting) for reusable
curl helpers.
