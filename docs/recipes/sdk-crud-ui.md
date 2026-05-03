# CRUD UI with the SDK Hooks

> Full list + create + update + delete page for a user-defined entity kind using @fastyoke/sdk 0.2.0.

# CRUD UI with the SDK Hooks

A single-page UI that lists, creates, updates, and deletes records of one entity kind — the pattern every real-data extension eventually reaches for. Uses the data hooks shipped in `@fastyoke/sdk@0.2.0`; no host-side Zustand, no custom fetch plumbing, no manual refresh on mutation.

> **Note**
>
> **For a fast standard-shape CRUD app, use the [Studio wizard](/docs/apps/studio-wizard) instead.** A 4-step builder (layout → branding → entities → review) ships a themed multi-page extension from your annotated entities — no code. Follow this recipe when you need bespoke CRUD UI in your own Next.js app outside the extension surface, or when you want to learn how the hooks fit together.

## What you'll build

A page that:

- Lists records of kind `contact` with paginated, server-side filtering.
- Opens an inline editor to create a new record.
- Opens the same editor seeded with a row's current values to update it.
- Deletes a row with an inline confirm strip.
- **Auto-refreshes** whenever another session mutates the same entity kind — realtime is on by default.

It runs as either an admin-shell feature or a loadable extension; the code below doesn't change either way because extensions see the same `FastYokeProvider` context the host mounts.

## Prerequisites

- SDK `^0.2.0` (`npm install @fastyoke/sdk`).
- The tenant has a registered entity kind named `contact`. Any kind works — swap the literal below.
- Your bundle has `data:read` + `data:write` in its manifest. The preview modal shows the inferred scopes before install; if they're not wide enough, the installer widens them when you confirm.

## Full component

```tsx
import { useState } from 'react';
import {
  useEntities,
  useCreateEntity,
  useUpdateEntity,
  useDeleteEntity,
  ApiError,
  type EntityResponse,
} from '@fastyoke/sdk';

type ContactPayload = {
  name: string;
  email: string;
};

export default function ContactsPage() {
  const [page, setPage] = useState(1);
  const [editing, setEditing] = useState<EntityResponse | null>(null);
  const [creating, setCreating] = useState(false);

  const { data, loading, error } = useEntities('contact', {
    page,
    pageSize: 20,
  });

  return (
    <section className="space-y-4">
      <header className="flex items-center justify-between">
        <h1 className="text-xl font-semibold">Contacts</h1>
        <button
          className="rounded bg-blue-600 px-3 py-1.5 text-sm text-white"
          onClick={() => {
            setEditing(null);
            setCreating(true);
          }}
        >
          New contact
        </button>
      </header>

      {loading && <p className="text-sm text-gray-500">Loading…</p>}
      {error && (
        <p role="alert" className="rounded bg-red-50 p-3 text-sm text-red-800">
          Failed to load contacts: {error.message}
        </p>
      )}

      {data && (
        <>
          <ContactTable
            rows={data.records}
            onEdit={(row) => {
              setCreating(false);
              setEditing(row);
            }}
          />
          <Pager
            page={data.page}
            pageSize={data.page_size}
            total={data.total}
            onChange={setPage}
          />
        </>
      )}

      {(creating || editing) && (
        <ContactEditor
          initial={editing}
          onDone={() => {
            setCreating(false);
            setEditing(null);
          }}
        />
      )}
    </section>
  );
}
```

The list re-renders on every mutation because `useEntities` auto-subscribes to `entity_mutation` events for `entity_name === 'contact'`. No `refetch()` after create/update/delete is required.

### The editor

```tsx
function ContactEditor({
  initial,
  onDone,
}: {
  initial: EntityResponse | null;
  onDone: () => void;
}) {
  const isEdit = Boolean(initial);
  const [name, setName] = useState(
    (initial?.data_payload as Partial<ContactPayload>)?.name ?? '',
  );
  const [email, setEmail] = useState(
    (initial?.data_payload as Partial<ContactPayload>)?.email ?? '',
  );

  const { createEntity, loading: creating } = useCreateEntity();
  const { updateEntity, loading: updating } = useUpdateEntity();
  const [err, setErr] = useState<string | null>(null);

  const busy = creating || updating;

  async function save() {
    setErr(null);
    try {
      if (isEdit && initial) {
        await updateEntity({
          kind: 'contact',
          id: initial.id,
          dataPayload: { name, email },
        });
      } else {
        await createEntity({
          kind: 'contact',
          dataPayload: { name, email },
        });
      }
      onDone();
    } catch (e) {
      setErr(e instanceof ApiError ? e.message : 'Unknown error');
    }
  }

  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        void save();
      }}
      className="space-y-2 rounded border p-4"
    >
      <h2 className="font-medium">{isEdit ? 'Edit contact' : 'New contact'}</h2>
      <label className="block">
        Name
        <input
          value={name}
          onChange={(e) => setName(e.target.value)}
          className="block w-full rounded border px-2 py-1"
          required
        />
      </label>
      <label className="block">
        Email
        <input
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          className="block w-full rounded border px-2 py-1"
          required
        />
      </label>
      {err && (
        <p role="alert" className="text-sm text-red-700">
          {err}
        </p>
      )}
      <div className="flex gap-2">
        <button
          type="submit"
          disabled={busy}
          className="rounded bg-blue-600 px-3 py-1.5 text-sm text-white disabled:opacity-50"
        >
          {busy ? 'Saving…' : 'Save'}
        </button>
        <button type="button" onClick={onDone} className="text-sm underline">
          Cancel
        </button>
      </div>
    </form>
  );
}
```

`useCreateEntity` / `useUpdateEntity` each expose a stable `createEntity` / `updateEntity` callback and an `{ loading, error, result }` state triple. Mutations here fire no optimistic updates — `loading` holds until the server commit is durable, at which point the realtime broadcast refreshes the list.

### The delete button

Inline strip on each row in `ContactTable`. Hard delete — no soft tombstone.

```tsx
function DeleteButton({ id }: { id: string }) {
  const { deleteEntity, loading } = useDeleteEntity();
  const [confirm, setConfirm] = useState(false);

  if (!confirm) {
    return (
      <button className="text-sm text-red-700" onClick={() => setConfirm(true)}>
        Delete
      </button>
    );
  }
  return (
    <span className="space-x-2 text-sm">
      <span>Delete?</span>
      <button
        disabled={loading}
        onClick={() => deleteEntity({ kind: 'contact', id })}
        className="text-red-700"
      >
        Yes
      </button>
      <button onClick={() => setConfirm(false)}>No</button>
    </span>
  );
}
```

When `deleteEntity` resolves, the realtime broadcast fires and the parent `useEntities` hook refetches — the deleted row disappears from the table automatically.

## Not shown: `ContactTable` and `Pager`

Plain JSX mapping over `data.records` and a five-button pager. Both are uninteresting; paste any implementation you already use.

## When to opt out of realtime

The default (`realtime: true`) is right for every *list* that's visible to a user. Opt out when:

- You're building a snapshot report the user triggers explicitly (`Refresh` button wired to the hook's returned `refetch`).
- You're running hundreds of `useEntity(kind, id)` instances in the same page — each registers a listener on the shared socket, and the redraw storm on a mass update hurts more than polling would.

```tsx
useEntities('contact', { pageSize: 500 }, { realtime: false });
```

## Scope implications

The [scope inference pass](/docs/apps/advanced#real-data-extensions-sdk-020) reads this file and lands the bundle with `["data:read", "data:write"]`. Don't fight it — the admin sees the inferred list before install and confirms explicitly. If you remove the write hooks later the scopes narrow automatically on re-generate.

## Related

- [SDK reference — data hooks](/docs/sdk/reference#react-data-hooks)
- [SDK reference — realtime](/docs/sdk/reference#realtime)
- [Advanced App Builder — real-data extensions](/docs/apps/advanced#real-data-extensions-sdk-020)
- [Extension authoring](/docs/recipes/extension-authoring) — scaffold → build → install a bundle that includes this page.
