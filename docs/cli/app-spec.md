# fy-app.json Spec

> The versioned source-of-truth contract that fy app create and fy app add-entity read and write.

# `fy-app.json` Spec

Every project scaffolded by `fy app create` carries a single
`fy-app.json` file at its root. This file is the **source of truth**
for the project's entities and FSM workflows — `fy app add-entity`
reads it, validates the proposed change against the same schema,
and writes the merged spec back. `manifest.json`, the Zod schemas,
the React-Query hooks, and the CRUD pages are all *derived* from
it; `fy-app.json` itself is the artifact you should hand to an
LLM agent (or commit to git) when you want a stable, structured
description of the app.

The schema is versioned. Today the only supported version is
`"1"`. Future incompatible shape changes will bump the version
and ship a migration path in the CLI; old projects continue to
build until they're explicitly migrated.

## Top-level shape

```json
{
  "fy_app_spec_version": "1",
  "name": "my-orders",
  "extension_id": "example.my-orders",
  "entities": [
    {
      "name": "Order",
      "fields": [
        { "name": "ref",    "type": "string" },
        { "name": "amount", "type": "number" }
      ],
      "fsm": {
        "initial_state": "pending",
        "states": ["pending", "fulfilled", "cancelled"],
        "transitions": [
          { "from": "pending", "to": "fulfilled", "event": "fulfill", "guard": null },
          { "from": "pending", "to": "cancelled", "event": "cancel",  "guard": null }
        ]
      }
    }
  ]
}
```

| Field | Type | Notes |
|---|---|---|
| `fy_app_spec_version` | literal `"1"` | Bumped on incompatible schema changes. |
| `name` | non-empty string | Used as the npm package name in the scaffolded `package.json`. |
| `extension_id` | non-empty string | Identifier the FastYoke host registers the extension under. `fy app create` derives this as `example.<slug>` from the project name; edit it before publishing to anything real. |
| `entities` | array, min length 1 | One or more entities. Append-only via `fy app add-entity`. |

## `entities[]`

| Field | Type | Notes |
|---|---|---|
| `name` | string | **PascalCase** — used to generate type names, file names, and URL paths (`Order` → `OrderList.tsx`, `/orders`, `OrderSchema`). |
| `fields` | array | May be empty. Order is preserved into the React form. |
| `fsm` | object | Per-entity finite state machine. See below. |

### `entities[].fields[]`

| Field | Type | Notes |
|---|---|---|
| `name` | string | Used verbatim as the property name on the entity type and Zod schema. |
| `type` | enum | One of `string`, `number`, `boolean`, `date`. The enum is closed in v1; new types require a spec-version bump. |

### `entities[].fsm`

| Field | Type | Notes |
|---|---|---|
| `initial_state` | string | Must appear in `states`. Enforced by the Zod schema. |
| `states` | array, min length 1 | Distinct state names. The CLI imposes a soft cap of **50 states** per entity (see `fy-feature-schema.json`). |
| `transitions` | array | May be empty. See below. |

### `entities[].fsm.transitions[]`

| Field | Type | Notes |
|---|---|---|
| `from` | string | Source state. Must appear in `states`. |
| `to` | string | Target state. May equal `from` — **self-loops are legal** and are how you model audit-only events, idempotent retries, and payload counters. See [FSM Designer → self-loops](/docs/workflows). |
| `event` | string | The event name that fires the transition. Required — the CLI rejects two-part `from:to` triplets. |
| `guard` | object \| null | A JSONLogic predicate, or `null` for an unguarded transition. **JSONLogic is the only guard kind supported in v1** (see `fy-feature-schema.json`); the runtime evaluates it through the sandboxed `jsonlogic-rs` evaluator, never via raw string parsing. |

> **Append-only in practice**
>
> <code>fy app add-entity</code> never edits an existing entity —
>   it only appends. To change an entity's fields or FSM after
>   scaffold, edit <code>fy-app.json</code> by hand and re-run
>   whichever generator you need; the entity's <code>.schema.ts</code>{' '}
>   + page files are pure functions of the spec and safe to
>   regenerate.

## Headless equivalents

Every prompt in `fy app create` maps to a flag. The flags accept
the same comma-delimited mini-DSL that the prompts use, so any
spec describable in the interactive wizard is also expressible
headlessly:

| Spec slot | Flag | Mini-DSL |
|---|---|---|
| entity name | `--entity ` | PascalCase identifier |
| `fields[]` | `--fields "<pairs>"` | `name:type,name:type` (types `string` / `number` / `boolean` / `date`) |
| `states[]` | `--states "<names>"` | `s1,s2,s3` — the value of `--initial` is auto-added if missing |
| `initial_state` | `--initial <state>` | single state name; required whenever `--states` is set |
| `transitions[]` | `--transitions "<triplets>"` | `from:to:event,from:to:event` — all three parts required, `guard` is always `null` in flag-driven mode |

For guards, edit `fy-app.json` after scaffolding — the wizard
does not collect them.

## Feature schema

`fy-feature-schema.json` ships alongside `capabilities.json` in
the npm tarball and declares the *constraints* that the v1 spec
must satisfy:

```json
{
  "spec_version": "1",
  "field_types": ["string", "number", "boolean", "date"],
  "fsm_constraints": {
    "max_states": 50,
    "self_loops": true,
    "guard_types": ["jsonlogic"]
  }
}
```

An LLM agent authoring a spec can read this file to know which
field types and guard kinds are legal **without running the CLI**.
The path is also reported by `fy capabilities`:

```bash
fy capabilities | jq -r .feature_schema_path
# ./fy-feature-schema.json
```

## Validation errors

`fy app create` and `fy app add-entity` both validate the spec
through the same Zod schema before writing any files. Common
rejections:

- **`initial_state must be a member of states`** — you supplied
  `--initial review` but `--states "draft,published"` didn't
  include it. Add the initial state to `--states` or rely on
  the auto-add behaviour.
- **`Invalid transition format "draft:published" — expected from:to:event (all three parts required)`** —
  every transition triplet must have an event name.
- **`refusing to overwrite existing file(s): …`** (`fy app create` only) — the
  target directory already contains scaffold output. Move or
  delete the colliding files, or pick a different `name`.
- **`Entity '' already exists at src/entities/.schema.ts`**
  (`fy app add-entity` only) — the entity is already in the
  spec; pick a different name. Editing an existing entity is
  intentionally out of scope (see callout above).

See [Command Reference](/docs/cli/commands) for the full flag
list and exit-code behaviour.
