---
title: SDK Reference
summary: Public exports of @fastyoke/sdk — provider, data hooks, realtime client, FsmTimeline / FsmViewer, WorkflowHistory, SmartField, and the typed resource clients.
order: 2
---

# SDK Reference

The `@fastyoke/sdk` package is the public surface every host, extension, and external integration works against. TypeScript types in the package are authoritative — when this page and the `.d.ts` disagree, trust the types.

Current version: **0.2.0**. See the [CHANGELOG](https://github.com/versacomp/fastyoke-sdk/blob/main/CHANGELOG.md) for the 0.1.x → 0.2.0 delta.

## `<FastYokeProvider>`

Mounts the SDK context. The host app mounts one at the root with its auth-aware fetcher and the current tenant; extensions see the same context when loaded into the host, so extension code never mounts its own provider.

```tsx
import { FastYokeProvider } from '@fastyoke/sdk';

<FastYokeProvider
  tenantId="11111111-1111-1111-1111-111111111111"
  fetcher={apiFetch}
>
  <App />
</FastYokeProvider>
```

### Props

| Prop | Type | Required | Description |
|---|---|---|---|
| `tenantId` | `string` | yes | Current tenant UUID. Every request the SDK issues is scoped by this — see the [Multi-tenancy mandate](/docs/getting-started). |
| `fetcher` | `(input, init?) => Promise<Response>` | yes | Credential-aware fetch. The host wires its `apiFetch` (auto-attaches the JWT, handles 401 redirects); tests inject a mock. |
| `projectId` | `string \| null` | no | When set, narrows list queries and project-scoped mutations. |
| `baseUrl` | `string` | no | Absolute API origin. Defaults to the current origin — extensions loaded into the admin shell never set this. |
| `realtime` | `boolean` | no | Default `true`. When `false`, no WebSocket is opened and every realtime-aware hook behaves as if `{ realtime: false }` were passed individually. Useful for SSR and tests. |
| `socketFactory` | `(url: string) => WebSocket` | no | Override the `WebSocket` constructor. Tests inject a controllable fake; production leaves this unset. |

## `useFastYoke()`

Returns the current SDK context value. The six typed resource clients hang off it plus `realtime` (the shared WebSocket client) and the raw transport inputs.

```ts
const {
  tenantId,
  projectId,
  fetcher,
  schemas,
  jobs,
  entities,
  pages,
  files,
  extensions,
  realtime, // RealtimeClient | null
} = useFastYoke();
```

Throws if called outside a `<FastYokeProvider>`. Client instances are stable across renders unless `tenantId` / `projectId` / `fetcher` / `baseUrl` change.

## Resource clients

Every client method returns a typed `Promise`. Non-2xx responses throw [`ApiError`](#apierror) with the parsed body; network failures throw `TypeError` (the platform default for fetch).

### `entities`

```ts
entities.list(kind, params?): Promise<PagedEntityResponse>
entities.get(kind, id): Promise<EntityResponse>
entities.create(kind, dataPayload): Promise<EntityResponse>
entities.patch(kind, id, dataPayload): Promise<EntityResponse>
entities.delete(kind, id): Promise<void>
entities.exportPdf(kind, id): Promise<Blob>
```

`delete` is a hard delete — no soft-tombstone. Admins who need audit history run the entity kind through an FSM and read `event_log`.

### `jobs`

```ts
jobs.list(params?): Promise<JobResponse[]>
jobs.get(id): Promise<JobResponse>
jobs.create(input): Promise<JobResponse>
jobs.transition(id, input): Promise<JobResponse>
jobs.cancel(id, input): Promise<JobResponse>   // admin-only
jobs.history(id): Promise<EventLogEntry[]>
```

`cancel` is the [FSM administrative override](/docs/workflows) — it bypasses guard evaluation and stamps an `__admin_cancel__` row on `event_log` with the supplied reason.

### `schemas`, `pages`, `files`, `extensions`

Identical shape (list / get / create / patch / delete as applicable). See the `.d.ts` files in the installed package for per-method signatures.

## React data hooks

Thin async wrappers over the clients, useful when the surrounding component wants `{ loading, error, data }` state without hand-rolling effects.

Two shapes:

- **Read hooks** return `{ data, loading, error, refetch }`. Fire on mount and whenever dependencies change; `AbortSignal`-gated cancel means post-unmount or stale results never update state.
- **Action hooks** return `{ <verb>, loading, error, result }`. `<verb>` is a stable callback you invoke from a handler; `result` holds the last successful response, `error` the last thrown `ApiError`.

No optimistic updates — `loading` stays `true` until the round-trip completes. Zustand coupling is deliberately absent so extensions don't see the host's store choices.

### Read hooks

| Hook | Returns | Realtime match |
|---|---|---|
| `useEntities(kind, filters?, options?)` | `PagedEntityResponse` | `entity_mutation` where `entity_name == kind` |
| `useEntity(kind, id, options?)` | `EntityResponse` | `entity_mutation` where `entity_name == kind && record_id == id` |
| `useJobs(params?, options?)` | `JobResponse[]` | any `transition` |
| `useJob(id, options?)` | `JobResponse` | `transition` where `job_id == id` |
| `useJobHistory(id, options?)` | `EventLogEntry[]` | `transition` where `job_id == id` |
| `useSchemas(params?)` | `SchemaResponse[]` | — no realtime (schemas don't broadcast) |
| `useSchema(id)` | `SchemaResponse` | — |
| `useActiveSchemas(params?)` | `SchemaResponse[]` (filtered) | — |

`options` is `{ realtime?: boolean }` (default `true`). See [Realtime](#realtime) below for how auto-refetch works.

### Action hooks

| Hook | Verb | Args | Result |
|---|---|---|---|
| `useCreateEntity()` | `createEntity` | `{ kind, dataPayload }` | `EntityResponse` |
| `useUpdateEntity()` | `updateEntity` | `{ kind, id, dataPayload }` | `EntityResponse` |
| `useDeleteEntity()` | `deleteEntity` | `{ kind, id }` | `true` on success |
| `useSpawnJob()` | `spawnJob` | `CreateJobInput` | `JobResponse` |
| `useTransitionJob()` | `transitionJob` | `{ id, input }` | `JobResponse` |
| `useCancelJob()` | `cancelJob` | `{ id, input }` | `JobResponse` |

### Worked examples

**Paginated entity list with optimistic-free create button:**

```tsx
import { useEntities, useCreateEntity } from '@fastyoke/sdk';

function WidgetsPanel() {
  const { data, loading, error, refetch } = useEntities('widget', {
    page: 1,
    pageSize: 20,
  });
  const { createEntity, loading: creating } = useCreateEntity();

  if (loading) return <p>Loading widgets…</p>;
  if (error) return <p>Failed: {error.message}</p>;

  return (
    <section>
      <ul>
        {data?.records.map((r) => (
          <li key={r.id}>{String(r.data_payload.name)}</li>
        ))}
      </ul>
      <button
        disabled={creating}
        onClick={async () => {
          await createEntity({
            kind: 'widget',
            dataPayload: { name: 'New widget' },
          });
          refetch(); // or rely on realtime auto-refresh — see below
        }}
      >
        {creating ? 'Creating…' : 'Add widget'}
      </button>
    </section>
  );
}
```

**Live workflow timeline via `useJob` + `<WorkflowHistory>`:**

```tsx
import { useJob, WorkflowHistory } from '@fastyoke/sdk';

function JobPage({ id }: { id: string }) {
  const { data: job } = useJob(id);
  return (
    <>
      <h1>{job?.current_state ?? '…'}</h1>
      <WorkflowHistory jobId={id} />
    </>
  );
}
```

Both the header and the history refresh automatically when the backend broadcasts a transition for `id` — no polling, no manual refetch.

**Transition button:**

```tsx
import { useTransitionJob } from '@fastyoke/sdk';

function ApproveButton({ jobId }: { jobId: string }) {
  const { transitionJob, loading, error } = useTransitionJob();
  return (
    <>
      <button
        disabled={loading}
        onClick={() =>
          transitionJob({ id: jobId, input: { eventType: 'approve' } })
        }
      >
        Approve
      </button>
      {error && <span role="alert">{error.message}</span>}
    </>
  );
}
```

## `<WorkflowHistory>`

Drop-in table over `useJobHistory`. Humanizes `__created__` / `__admin_cancel__` sentinels, em-dashes missing actor/reason, and ships with inline styling so iframe-isolated extensions render without the host stylesheet.

```tsx
<WorkflowHistory jobId="b9c..." />
```

### Props

| Prop | Type | Description |
|---|---|---|
| `jobId` | `string` | Required. Drives `useJobHistory(jobId)` internally. |
| `formatTimestamp` | `(iso: string) => string` | Override the default `toLocaleString()`. |
| `className` | `string` | Applied to the outer `<table>`. |
| `style` | `React.CSSProperties` | Merged with the inline defaults. |

## `<FsmTimeline>` + `<FsmViewer>`

Drop-in workflow visualizers for ISVs embedding FastYoke flows. Two physically-separate exports so tree-shaking is deterministic — `FsmTimeline` ships with **zero** reactflow / elkjs imports.

### `<FsmTimeline>` — standalone

Pure HTML / Tailwind utility classes. Mobile-friendly; right-sized for L1 support and field-tech surfaces.

```tsx
import { FsmTimeline, useJob, useJobHistory } from '@fastyoke/sdk';

const { data: job } = useJob(jobId);
const { data: history } = useJobHistory(jobId);
const { data: schema } = useSchema(job?.schema_id);

<FsmTimeline
  schema={schema?.schema_json ?? { initial_state: 'pending' }}
  entity={job ? { current_state: job.current_state, history } : undefined}
  onTransitionRequest={async (target) => {
    // Translate target state → event_type via the schema's transitions list,
    // then fire the host's transition mutation.
  }}
/>
```

### `<FsmViewer>` — composed shell

Renders the timeline plus a `React.lazy`-imported reactflow canvas. Smart-default mode: `entity` supplied → operator (timeline only); `entity` omitted → engineer (canvas only). Pass `mode='dual'` for both surfaces side-by-side, or `mode='operator' | 'engineer'` to lock the surface and hide the built-in mode switcher.

```tsx
<FsmViewer schema={schema} entity={entity} onTransitionRequest={doTransition} />
```

### Composable types

The viewer is **transition-agnostic**: the host composes `EntityState = { current_state, history? }` from `/tenant/jobs/:id` + the event-log endpoint and translates the chosen target state to a backend `event_type` itself. The viewer never fetches and never writes.

| Export | Purpose |
|---|---|
| `EntityState` | `{ current_state, history? }` shape the viewer reads. |
| `EntityHistoryEntry` | Subset of `EventLogEntry` the viewer displays. |
| `ViewerSchema` | Subset of `SchemaDefinition` (initial state + transitions). |
| `ViewerTransition` | `{ from, to, event_type }`. |
| `TransitionRequestHandler` | `(targetState, payload?) => Promise<void>`. |

### Audit-diff disclosures

`FsmTimeline` accepts a `renderHistoryDetail(entry, index) => ReactNode | null` render-prop that renders an inline `<details>` disclosure under each history row. Pair with `<FsmAuditDiff />` and the `matchAuditEntry()` helper to surface payload before/after diffs straight from the audit ledger:

```tsx
import {
  FsmTimeline,
  FsmAuditDiff,
  matchAuditEntry,
  useJobAudit,
  useJobHistory,
  useJob,
  useSchema,
} from '@fastyoke/sdk';

function MyJobView({ jobId }: { jobId: string }) {
  const { data: job } = useJob(jobId);
  const { data: history } = useJobHistory(jobId);
  const { data: audit } = useJobAudit(jobId);
  const { data: schema } = useSchema(job?.schema_id ?? '');

  if (!job || !schema) return null;

  return (
    <FsmTimeline
      schema={schema.schema_json}
      entity={{ current_state: job.current_state, history: history ?? [] }}
      renderHistoryDetail={(entry) => {
        const row = matchAuditEntry(audit ?? [], entry);
        return row ? <FsmAuditDiff audit={row} changesOnly /> : null;
      }}
    />
  );
}
```

`FsmAuditDiff` renders a flat key-by-key shallow diff (added / removed / changed / unchanged). Pass `changesOnly` to hide unchanged keys. Returns a friendly placeholder when the audit row's payloads are null (job had no `context_record_id`) or when the snapshots are equal.

### Bundle hygiene

Importing only `FsmTimeline` does **not** pull in reactflow or elkjs — verified at build time by `frontend/sdk/__tests__/fsm-viewer.bundle.test.ts`. ISVs on strict bundle budgets (Vercel Pro frontends, mobile-first hosts) can ship the timeline at near-zero cost.

## Realtime

`@fastyoke/sdk` ships with a built-in WebSocket client that multiplexes two event streams — FSM transitions and entity CRUD — over a single connection per provider. Extensions don't open their own sockets; they subscribe to the shared one.

### How hooks use it

Every realtime-aware read hook accepts `{ realtime?: boolean }` (default `true`). When enabled, the hook registers a listener on the provider's shared client and calls `refetch()` when a matching event arrives — no polling, no backend changes from your side.

```tsx
// Default: refetches on any mutation to the 'widget' kind.
useEntities('widget');

// Opt out — only updates via explicit refetch().
useEntities('widget', undefined, { realtime: false });

// Narrow to a single record.
useEntity('widget', id);
```

### `RealtimeClient` directly

For rare cases where you want the raw event stream (e.g. a debug overlay, a metrics push), import `RealtimeClient` from the package:

```ts
import { RealtimeClient, type RealtimeEvent } from '@fastyoke/sdk';

const client = new RealtimeClient({ tenantId });
const unsubscribe = client.subscribe((ev: RealtimeEvent) => {
  if (ev.kind === 'transition') {
    console.log(ev.job_id, ev.from_state, '→', ev.to_state);
  } else if (ev.kind === 'entity_mutation') {
    console.log(ev.op, ev.entity_name, ev.record_id);
  }
});

// later…
unsubscribe();
client.close();
```

### Envelope shapes

Every message is a `kind`-tagged envelope. Route by `kind`, not field presence.

```ts
type TransitionRealtimeEvent = {
  kind: 'transition';
  tenant_id: string;
  job_id: string;
  schema_id: string;
  event_type: string;
  from_state: string | null;
  to_state: string;
};

type EntityMutationRealtimeEvent = {
  kind: 'entity_mutation';
  tenant_id: string;
  entity_name: string;
  record_id: string;
  op: string; // "create" | "update" | "delete"
};
```

### Reconnection

Exponential backoff (1s → 2s → 4s …, capped at 30s). The delay resets on a successful connection. The client doesn't replay missed events from the disconnect window — hooks refetch on reconnect via their own dependency arrays, which is idempotent.

## Extension registry

Host-side helpers for loading, activating, and rendering tenant-uploaded extension bundles. Extension authors don't typically touch these.

### `<ExtensionProvider>`

```tsx
import { ExtensionProvider, ExtensionErrorBoundary } from '@fastyoke/sdk';

<ExtensionProvider>
  <ExtensionErrorBoundary>
    <AdminShell />
  </ExtensionErrorBoundary>
</ExtensionProvider>
```

### `useExtensionRegistry()`

```ts
interface ExtensionRegistryValue {
  loaded: LoadedExtension[];
  componentsByBlockType: Map<string, ComponentType>;
  pagesByPath: Map<string, ComponentType>;
  customBlocks: CustomBlockDescriptor[];
  loading: boolean;
  refresh: () => void;
}
```

Host surfaces call `refresh()` after the admin installs / activates / deactivates an extension so the Page Designer dropdown and custom page routes pick up the new set without a reload.

## `ApiError`

Thrown from any client method or action hook on a non-2xx response.

```ts
import { ApiError } from '@fastyoke/sdk';

try {
  await jobs.transition(id, { eventType: 'bad' });
} catch (err) {
  if (err instanceof ApiError) {
    console.log(err.status, err.body); // server's parsed error body
  }
  // else: TypeError (network failure, CORS, etc.)
}
```

Action hooks surface the same error via their `error` field while still re-throwing from the returned promise — you can branch on `err.status` at the call site or let the effect that reads `error` handle it.

## Zod schemas

Every wire DTO has a matching Zod schema exported alongside the TypeScript type:

```ts
import {
  SchemaResponseZ,
  JobResponseZ,
  EventLogEntryZ,
  EntityResponseZ,
  PageResponseZ,
  ExtensionManifestZ,
  ExtensionResponseZ,
  MintTokenResponseZ,
  SchemaDefinitionZ,
} from '@fastyoke/sdk';

const parsed = JobResponseZ.parse(await response.json());
```

The SDK uses these internally on every deserialize path. Re-exported so extension code that talks to custom backends can validate against the same contract.

## `FileRef` discriminator

`data_payload` fields that reference uploaded files use a JSON-level tagged marker. Helpers for the convention:

```ts
import { isFileRef, extractFileId, type FileRef } from '@fastyoke/sdk';

if (isFileRef(val)) {
  const id = extractFileId(val); // uuid of the file row
}
```

## `<SmartField />` (LCAP)

Single React component that renders one annotated entity field
— picks the input from the closed 9-type vocabulary, applies
display formatting from `ui_config_json`, runs zod validation
on change. Used by Forms v2, the Page Designer `entity_field`
block, the CRUD scaffold's emitted bundles, and any
`@fastyoke/next` consumer.

```tsx
import { SmartField, type EntityFieldAnnotation } from '@fastyoke/sdk';

<SmartField
  annotation={annotation}                    // EntityFieldAnnotation
  value={record.data_payload[field_key]}     // current value
  onChange={(next) => update(field_key, next)}
  mode="edit"                                // 'edit' | 'display'
  density="comfortable"                      // 'compact' | 'comfortable' | 'spacious'
  uiConfigOverride={{ '@ui/date_format': 'yyyy' }}  // optional
  currentTier="team"                         // 'hobby'|'pro'|'team'|'enterprise'|'fleet'
/>
```

Props:

| Prop | Type | Required | Notes |
|---|---|---|---|
| `annotation` | `EntityFieldAnnotation` | yes | The row from `/tenant/entities/:name/annotations`. Drives every resolution decision. |
| `value` | unknown | yes | Current field value. Caller owns the source of truth. |
| `onChange` | `(next: unknown) => void` | yes | Called on every change. Caller debounces / batches as desired. No-op on `mode="display"`. |
| `mode` | `'edit' \| 'display'` | optional | Defaults to `'edit'`. `'display'` short-circuits to a `<span>` with the formatted value — no input mounts. |
| `density` | `'compact' \| 'comfortable' \| 'spacious'` | optional | Forwarded to the catalog component. |
| `uiConfigOverride` | `Record<string, unknown>` | optional | `@ui/*` keys merged AFTER `annotation.ui_config_json`. Lets one block specialize one rendering surface. |
| `currentTier` | tier string | optional | Defaults to `'team'`. Below Team, expression keys (`@ui/visible_when`, etc.) short-circuit with a console warn. |
| `className`, `id`, `exprContext` | various | optional | Standard pass-through props. |

Imports:

```ts
import {
  SmartField,
  type SmartFieldProps,
  type SmartFieldMode,
  type SmartFieldDensity,
  // Pure helpers — testable without React.
  resolveSmartField,
  resolveFieldType,
  type ResolvedFieldType,
  type ResolvedSmartField,
  // Annotation row + zod adapter.
  type EntityFieldAnnotation,
  type LcapFieldType,
  entityAnnotationToZod,
} from '@fastyoke/sdk';
```

See the [LCAP section](/docs/lcap) for the full annotation
model + matrix. `<SmartField />` is framework-agnostic React-DOM;
the `@fastyoke/next` SDK adds an SSR wrapper for App-Router
consumers.

## `entityAnnotationToZod()`

Build a zod object schema from an array of annotation rows.
Used by the CRUD scaffold's emitted bundles to
validate form input client-side before the write hook fires.

```ts
import { entityAnnotationToZod } from '@fastyoke/sdk';

const schema = entityAnnotationToZod(annotations);
const result = schema.safeParse(formValues);
if (!result.success) showErrors(result.error);
```

The helper honors `required` / `max_length` / `min` / `max` /
`options_json` / `field_type` / `ui_config_json` and produces
the right zod leaf per type. See [field types](/docs/lcap/field-types)
for the full refinement table.

## Related

- [Quickstart](/docs/sdk/quickstart) — minimum viable extension in ~30 lines.
- [Extension authoring recipe](/docs/recipes/extension-authoring) — scaffold → build → publish.
- [CRUD UI recipe](/docs/recipes/sdk-crud-ui) — full create/read/update/delete page using the hooks above.
- [Advanced App Builder](/docs/apps/advanced) — LLM-authored extensions that consume this SDK.
- [LCAP](/docs/lcap) — annotation-driven entity rendering.
