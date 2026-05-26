---
title: Command Reference
summary: Every subcommand for fy (extension authoring).
order: 1
---

# Command Reference

## `@fastyoke/cli` / `fy` (extension authors)

The public npm package ships a single binary named **`fy`**. The
subcommands cover three workflows:

- **Extension authoring** (`init` / `dev` / `build` / `publish`)
  — scaffold a single React extension and ship it to a tenant.
- **App scaffolding** (`app create` / `app add-entity`) — generate
  a full app extension from an entity + FSM spec, including Zod
  schemas, typed React-Query hooks, and CRUD pages.
- **Discovery** (`capabilities`) — emit a JSON manifest of every
  command, flag, and FSM constraint for AI agents and tooling.

::code-group
```bash title="global"
npm install -g @fastyoke/cli
fy --help
```

```bash title="local"
npm install --save-dev @fastyoke/cli
npx fy --help
```
::

### `fy init [name] [--template <kind>]`

Scaffold a new project in the current directory (or in `name/` if
provided). Three template variants:

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

- **`--template vite-spa`** — a standalone tenant-operator portal as a
  pure SPA. Vite + React + TypeScript + Tailwind + the FastYoke React
  SDK, pre-wired with a login screen, JWT-aware Fetcher,
  `<FastYokeProvider>`, an entity CRUD page on the `contact` kind, a
  file upload + download panel, and a realtime jobs list. Ships a
  Cloudflare-Pages-ready `public/_headers` file with a starter CSP
  that blocks third-party scripts by default. Driven by
  `npm run dev` / `npm run build` directly — `fy` lifecycle commands
  do not wrap it.

```bash
# Default — extension scaffold.
fy init my-extension

# Standalone consumer app.
fy init my-portal --template nextjs
cd my-portal
cp .env.local.example .env.local      # set FASTYOKE_TENANT_URL
npm install
npm run dev

# Standalone tenant-operator SPA for Cloudflare Pages.
fy init my-portal --template vite-spa
cd my-portal
cp .env.local.example .env.local      # set VITE_FASTYOKE_API_URL
npm install
npm run dev
```

The Next.js scaffold uses the **public submission token** for its
form route (`/forms/<token>`), not the form's slug. The token is
what `fy` shows you when you publish a form — keep it opaque.

The Vite SPA scaffold uses the **operator JWT** (obtained at runtime
by the user signing in at `/auth/login`) and stores it in
`localStorage`. See the
[SPA on Cloudflare Pages recipe](/docs/recipes/spa-on-cloudflare-pages)
for the full walk-through including the CORS allowlist edit and the
Cloudflare Pages dashboard steps.

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

::callout{type="warn" title="No generic API surface"}
`fy` only covers extension authoring and app scaffolding. There are no
`fy schemas list` / `fy jobs transition` / `fy entities get`
commands. If you need to call the tenant API from CI, use plain
`curl` / `httpx` with a tenant JWT — see [Authentication](/docs/auth).
::

### `fy app create [name]`

Scaffold a full **app extension** from a single entity + FSM
spec. Where `fy init` gives you an empty React extension shell,
`fy app create` generates a typed, working CRUD app you can
publish unchanged: Zod schemas, React-Query hooks, list / detail
/ form pages, a dashboard, and the manifest wiring.

Interactive by default — every prompt has a matching `--flag`
for headless / AI-agent use. With all four spec flags supplied
plus `--yes`, the command writes the project tree without
prompting.

```bash
# Interactive — prompts for entity name, fields, states, transitions.
fy app create my-orders

# Headless — same spec, no prompts.
fy app create my-orders \
  --entity Order \
  --fields "ref:string,amount:number" \
  --states "pending,fulfilled,cancelled" \
  --initial pending \
  --transitions "pending:fulfilled:fulfill,pending:cancelled:cancel" \
  --yes
```

| Flag | Description |
|---|---|
| `--entity <Name>` | Entity name in **PascalCase** (e.g. `Order`, `Shipment`). |
| `--fields <pairs>` | Comma-separated `name:type` pairs. Types: `string` / `number` / `boolean` / `date`. |
| `--states <names>` | Comma-separated FSM state names. Must include `--initial`. |
| `--initial <state>` | Initial FSM state. Required whenever `--states` is set. |
| `--transitions <list>` | Comma-separated `from:to:event` triplets. All three parts are required. |
| `-y, --yes` | Skip the final "Scaffold this app?" confirmation. |
| `--json` | Emit a machine-readable summary to **stdout**; human progress text stays on **stderr**. |

The generated `fy-app.json` is the source of truth — every other
file in the tree is derived from it. See
[the `fy-app.json` spec](/docs/cli/app-spec) for the full
schema, the closed field-type enum, and the FSM constraints.

Files written (one entity → 11 files; each extra entity adds
5 more):

```
my-orders/
├── fy-app.json              # source of truth — round-trippable spec
├── manifest.json            # extension manifest for the host
├── package.json
├── tsconfig.json
├── README.md
├── AGENTS.md                # quick map of the scaffold for LLM agents
└── src/
    ├── index.tsx                       # default export: { pages }
    ├── pages/Dashboard.tsx
    ├── components/FsmStatePanel.tsx
    ├── components/EventLog.tsx
    └── entities/
        ├── Order.schema.ts             # exports OrderSchema + Order type
        └── useOrders.ts                # useOrders(), useOrder(id), useCreateOrder(), useTransitionOrder()
    └── pages/
        ├── OrderList.tsx
        ├── OrderDetail.tsx
        └── OrderForm.tsx
```

The command refuses to overwrite existing files — re-running
inside a populated directory exits non-zero with the colliding
paths listed.

::callout{type="info" title="JSON mode for AI agents"}
Pass `--json` with `--yes` to drive the scaffold from another program. stdout is one JSON object with `status`, `files_written`, and `next_steps`; all human-readable progress is on stderr so the two streams never interleave.
::

### `fy app add-entity <Name>`

Extend an existing app project with a second (or third, etc.)
entity. Reads the project's `fy-app.json`, validates the new
entity against the same spec schema, then writes five new files
for the entity and **updates** four existing ones
(`fy-app.json`, `manifest.json`, `AGENTS.md`, `src/index.tsx`,
`src/pages/Dashboard.tsx`).

Must be run from inside a directory that already contains an
`fy-app.json`; otherwise the command exits with a message
pointing you at `fy app create`.

```bash
cd my-orders

fy app add-entity Driver \
  --fields "name:string,license:string" \
  --states "available,on_route,off_duty" \
  --initial available \
  --transitions "available:on_route:assign,on_route:available:complete" \
  --yes
```

Flags match `fy app create` except `--entity` (the name is a
positional argument) and there is no `--json` — this command is
intended for interactive iteration. Add-entity is non-destructive:
if `src/entities/<Name>.schema.ts` already exists, the command
refuses rather than overwriting it.

### `fy capabilities`

Print a JSON manifest of every `fy app` subcommand, every flag,
and the path to `fy-feature-schema.json` (which declares the
allowed field types, the per-app max state count, and the
supported guard kinds). Designed for LLM agents and IDE tooling
that want to discover what the installed CLI can do without
parsing `--help` output.

```bash
fy capabilities | jq .

# {
#   "cli_version": "0.2.0",
#   "commands": [
#     { "name": "app create",     "description": "...", "flags": [...] },
#     { "name": "app add-entity", "description": "...", "flags": [...] }
#   ],
#   "feature_schema_path": "./fy-feature-schema.json"
# }
```

The same `capabilities.json` is shipped inside the npm tarball,
so an agent can consult the schema without executing the binary
at all.

