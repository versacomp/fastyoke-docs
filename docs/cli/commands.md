# Command Reference

> Every subcommand for fy (extension authoring) and fastyoke-admin (operator ops).

# Command Reference

## `@fastyoke/cli` / `fy` (extension authors)

The public npm package ships a single binary named **`fy`**. Four
subcommands covering the extension-authoring loop — scaffold,
iterate, publish.

```bash title="global"
npm install -g @fastyoke/cli
fy --help
```

```bash title="local"
npm install --save-dev @fastyoke/cli
npx fy --help
```

### `fy init [name] [--template <kind>]`

Scaffold a new project in the current directory (or in `name/` if
provided). Two template variants:

- **`--template extension`** (default) — a FastYoke admin extension.
  Writes `manifest.json`, `package.json`, `tsconfig.json`,
  `src/index.tsx`, `README.md`, and `.gitignore`. The scaffold
  imports `react` + `@fastyoke/sdk` as externals so they resolve
  through the host's import map — same rule your extension follows
  at runtime. Build/dev/publish flow through `fy dev` / `fy build` /
  `fy publish`.

- **`--template nextjs`** — a standalone customer-facing Next.js app
  (App Router, TypeScript, Tailwind) pre-wired against a tenant's
  public form-submission and signed-URL PDF download endpoints.
  Generates ~14 files including a starter `/forms/[token]` route
  with a schema-driven renderer covering text / email / textarea /
  checkbox / signature field types. Driven by `npm run dev` /
  `npm run build` directly — `fy` lifecycle commands do not wrap
  it.

```bash
# Default — extension scaffold.
fy init my-extension

# Standalone consumer app.
fy init my-portal --template nextjs
cd my-portal
cp .env.local.example .env.local      # set FASTYOKE_TENANT_URL
npm install
npm run dev
```

The Next.js scaffold uses the **public submission token** for its
form route (`/forms/<token>`), not the form's slug. The token is
what `fy` shows you when you publish a form — keep it opaque.

### `fy dev`

Watch-mode esbuild against the scaffold's entry point. Rebuilds
`dist/bundle.mjs` on every source change. Intended for a live
development loop where you keep the admin shell open in one
window and edit code in another.

```bash
fy dev
# optionally override paths:
fy dev --entry src/main.tsx --outfile dist/out.mjs
```

### `fy build`

One-shot build (no watcher) of the same esbuild config. Use this
in CI before `fy publish`.

```bash
fy build
# outputs dist/bundle.mjs by default.
```

### `fy publish`

Upload the built bundle + manifest to a running FastYoke instance.
Uses the tenant admin JWT embedded in the `--token` argument — the
claims determine which tenant receives the upload.

```bash
fy publish \
  --tenant https://fastyoke.example \
  --token "$FASTYOKE_TOKEN" \
  --manifest ./manifest.json \
  --bundle ./dist/bundle.mjs
```

Goes through the same three-layer scanner (MIME magic → VT hash
→ LLM content) as a manual admin-shell upload. A `Malicious`
verdict is a hard reject; `Suspicious` / `Skipped` pass with
logging.

> **No generic API surface**
>
> <code>fy</code> only covers extension authoring. There are no
>   `fy schemas list` / `fy jobs transition` / `fy entities get`
>   commands. If you need to call the tenant API from CI, use plain{' '}
>   <code>curl</code> / <code>httpx</code> with a tenant JWT — see{' '}
>   <a href="/docs/auth">Authentication</a>.

## `fastyoke-admin` (operator)

Baked into the Docker image at `/app/fastyoke-admin`. Run via
`fly ssh console`. Credentials flow through environment variables
rather than argv where possible — `/proc/<pid>/cmdline` leaks on
Linux.

### Super-admin lifecycle

```bash
# Create the first super-admin on a fresh deploy. Idempotent
# bootstrap: running with an existing email updates the password.
ADMIN_EMAIL=you@example.com ADMIN_PASSWORD=<strong> \
  /app/fastyoke-admin create-admin

/app/fastyoke-admin list-admins

ADMIN_EMAIL=you@example.com ADMIN_PASSWORD=<new> \
  /app/fastyoke-admin reset-password

ADMIN_EMAIL=old@example.com \
  /app/fastyoke-admin delete-admin --yes       # --yes skips prompt
```

> **Web-UI equivalents**
>
> The platform-admin shell at <code>/super/admins</code> mirrors
>   every one of these — list / create / reset password / delete —
>   with the same last-admin and self-delete guards. The CLI remains
>   the right path for automation and for the initial bootstrap
>   (when no admin exists to log in).

### Backup / restore

```bash
# Rewrite /etc/litestream.yml from the current tenant list.
# Invoked by startup.sh before `litestream replicate` launches.
/app/fastyoke-admin regen-litestream-config --out /etc/litestream.yml

# Pull one tenant's SQLite back from Tigris into its on-disk path.
# For cold-restore recovery after volume loss — the app doesn't
# need to be running.
/app/fastyoke-admin restore-tenant --tenant <tenant_id>

# Move a live tenant to a different machine. Three-step
# orchestration: restore on target → update platform assignment
# → best-effort evict on source. Mints its own platform JWT from
# JWT_SECRET.
/app/fastyoke-admin reassign-tenant \
    --tenant <tenant_id> \
    --to-machine <machine_id> \
    --app-url http://localhost:8080
```

> **JWT_SECRET required for reassign**
>
> <code>reassign-tenant</code> mints a short-lived platform JWT to
>   authenticate its own HTTP calls to the app's ops endpoints.
>   Export <code>JWT_SECRET</code> in the shell you're running from
>   — already set inside the container.

> **Web-UI equivalents**
>
> <code>/super/tenants/:id</code> has a Danger Zone with{' '}
>   <strong>Restore from Tigris</strong> (pinned to the current owner)
>   and <strong>Reassign to machine</strong> (pinned to the target).
>   The CLI's <code>restore-tenant</code> is the right path for
>   post-volume-loss recovery when the app can't run; otherwise
>   the shell is the more convenient surface.

### Final cutover

Three-phase ops sequence. See
[DEPLOY_CHECKLIST.md](../auth) for the full runbook.

```bash
# Preflight — reports unmigrated tenant ids, exits non-zero
# when any remain. CI-friendly.
/app/fastyoke-admin phase13-status

# Destructive — drops the platform DB's tenant-scoped mirror
# tables + VACUUMs. Refuses to run when any tenant still has
# db_file_path IS NULL. Idempotent (DROP TABLE IF EXISTS).
/app/fastyoke-admin phase13-cutover           # dry run — prints plan
/app/fastyoke-admin phase13-cutover --confirm # executes
```

> **Web-UI equivalent**
>
> <code>/super/maintenance</code> shows the same preflight data
>   and provides a typed-confirmation cutover button. The CLI is
>   still the only way to run <code>phase13-status</code> from
>   CI for an automated readiness gate.

See [Authentication](/docs/auth) for how these tokens are validated
and the operator runbook in `DEPLOY_CHECKLIST.md` for the full
cutover + failover scenarios.
