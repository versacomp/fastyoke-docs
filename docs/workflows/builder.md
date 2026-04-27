# FSM Designer

> Draw the graph, wire transitions, compose guards, declare actions.

# FSM Designer

The builder is a React Flow canvas at `/admin/workflows/new` (or
`/admin/workflows/edit/:id`). Visual coordinates live in React
Flow; the logical FSM contract lives in a Zustand store. The two
stay segregated — when you save, the compiler walks the graph and
emits the strict `schema_json` the backend persists.

## States

Click **Add state**, give it a label. One state per FSM is marked
**initial** (the drop target for a job's spawn event).

> **Orphan warnings**
>
> The save-time compiler reports any state that has no connecting
>   transitions as an orphan warning. Nothing breaks — you just see
>   a banner reminding you to either wire it up or delete it.

## Transitions

Drag from one node's edge-handle to another. The transition
side-panel opens:

### Event type

Required. Snake-case identifier (e.g. `mark_processed`,
`request_approval`). No two transitions from the same source state
may share an event type — the compiler rejects duplicates.

### Guard (optional)

Toggle **Require guard** to expose the composer. The default
three-field form covers most cases:

- **Variable path** — JSONPath into the job's data.
- **Operator** — `==`, `!=`, `>`, `<`, `>=`, `<=`, or
  `contains` (for arrays).
- **Value** — literal to compare against.

> **Entity-bound schemas get annotations**
>
> If the workflow has an <code>entity_name</code> binding set, the
>   Variable path input grows a <code>&lt;datalist&gt;</code> of
>   annotated field keys from the target entity. Typing a free-form
>   path still works — the picker is a suggestion, not a constraint.
>   See <a href="/docs/entities">Entities</a> for how to author
>   annotations.

For guards outside the three-field form's expressivity, the
composer accepts a raw JSONLogic object in advanced mode.

### Actions

An ordered list of side effects fired on a successful transition:

| Action type | Purpose |
|---|---|
| `WEBHOOK` | POST a JSON payload to an external URL (encrypted credentials) |
| `UPDATE_ENTITY` | Patch the bound entity record |
| `BATCH_SPAWN` | Fan out jobs on a secondary schema (e.g. one per vehicle in a route) |
| `ALGORITHM_EXECUTE` | Run a first-party optimization (VRPTW, TSP) |
| `WASM_EXECUTE` | Execute a tenant-uploaded Wasm module in the sandbox |
| `GENERATE_PUBLIC_LINK` | Mint an access token and bind it to the current job |

Each action's `payload_template` is a JSON blob with
`{{variable}}` placeholders resolved against the job context at
fire time.

## Versioning

Saving always creates a new version. The most recent is
automatically `is_active`; historical rows stay around for the
audit trail. Jobs spawned under an older version keep running
their original schema — an in-flight job is never silently
migrated.

## UI aliases

The **Aliases** panel lets you remap platform vocabulary to
domain-specific terms (e.g. "Job" → "Delivery"). Aliases are
stored on the schema and applied to the public-facing workspace
pages that embed it.
