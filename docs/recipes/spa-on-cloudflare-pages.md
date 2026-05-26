---
title: SPA on Cloudflare Pages with the React SDK
summary: Build a standalone Vite + React SPA that signs in to FastYoke, CRUDs entities with realtime, uploads files, and deploys to Cloudflare Pages.
order: 7
---

# SPA on Cloudflare Pages with the React SDK

A step-by-step walk-through for the architecture
[CRUD UI with the SDK Hooks](/docs/recipes/sdk-crud-ui) carves out as
"your own app outside the extension surface": a custom-branded SPA at
your own domain, signed in to FastYoke with operator credentials,
deployed to Cloudflare Pages.

::callout{type="tip"}
**If you want a built-in admin surface** instead of a custom SPA, the
admin shell at `/admin` already gives you every feature this recipe
re-implements. Pick this recipe when you need your own brand, your own
domain, or a bespoke layout the admin shell does not offer.
::

## What you'll build

A single-page Vite + React + TypeScript + Tailwind app, deployed to
Cloudflare Pages, with:

- A login screen that hits `/auth/login` and stores the operator JWT.
- A `<FastYokeProvider>` that hands typed clients to every component.
- A **Contacts** tab — list, create, edit, delete records of the
  `contact` entity kind. Realtime — updates from other sessions appear
  automatically.
- A **Files** tab — upload via multipart, download via the SDK's
  `FilesClient`, scan status visible on every file.
- A **Jobs** tab — paginated, realtime-aware FSM job list.
- A `_headers` file that ships a strict starter Content-Security-Policy.

## Prerequisites

- Node 20 or newer.
- The FastYoke CLI installed: `npm install -g @fastyoke/cli`. See
  [CLI commands](/docs/cli/commands) for alternatives.
- A FastYoke deployment with at least one entity kind registered named
  `contact`. The recipe pins this literal — substitute as needed.
- A tenant operator account whose role carries `data:read`,
  `data:write`, `files:read`, `files:write`, and `jobs:read` scopes
  (the default `operator` and `admin` roles do).
- A GitHub account.
- A Cloudflare account.

## 1. Scaffold the app

```bash
fy init my-portal --template vite-spa
cd my-portal
cp .env.local.example .env.local       # then edit VITE_FASTYOKE_API_URL
npm install
npm run dev
```

That writes a complete file tree — see
[`fy init`](/docs/cli/commands#fy-init-name---template-kind) for the
full list. Open <http://localhost:5173>; you should see the login
screen.

::callout{type="info" title="Why Vite, not Next.js?"}
Next.js would push you toward server-rendered routes you do not need
for an authenticated SPA, and complicates the auth story (where does
the JWT live during SSR?). Pure Vite ships exactly the browser code
the operator runs, nothing else.
::

## 2. Tour: login + JWT handling

The scaffold's `src/auth.ts` is the entire wire-level auth layer:

```ts title="src/auth.ts"
const KEY = 'fy.jwt';

export function getToken(): string | null {
  return localStorage.getItem(KEY);
}

export function setToken(token: string): void {
  localStorage.setItem(KEY, token);
}

export function clearToken(): void {
  localStorage.removeItem(KEY);
}

export async function login(
  apiUrl: string,
  email: string,
  password: string,
): Promise<string> {
  const res = await fetch(`${apiUrl}/api/v1/auth/login`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password }),
  });
  if (!res.ok) throw new Error(`Login failed (${res.status})`);
  const { token } = await res.json();
  setToken(token);
  return token;
}
```

::callout{type="warn" title="The JWT lives in localStorage"}
Any JavaScript the page loads — analytics, embeds, a compromised npm
dep — can read this token. The scaffolded `public/_headers` ships a
CSP that blocks third-party scripts by default. Audit any addition to
`script-src` carefully; if you need a stricter posture, a future
recipe will cover the BFF Worker pattern.
::

`src/useAuth.ts` is the React hook that ties this to the UI. On mount
it reads the stored token (if any) and calls `/auth/me` to recover the
`tenant_id` — the SDK's `<FastYokeProvider>` needs it. A non-2xx
response clears the token and drops the reader back to the login
screen.

The hook also listens for a `'fy:auth-expired'` event. The
`authFetcher` (next section) dispatches this on any 401 from any SDK
call — that way an expired token surfaces immediately instead of on
the next manual refresh.

## 3. Tour: the JWT-aware `Fetcher`

The bridge between localStorage and the SDK is `src/authFetcher.ts`:

```ts title="src/authFetcher.ts"
import type { Fetcher } from '@fastyoke/sdk';
import { clearToken, getToken } from './auth';

export function makeAuthFetcher(): Fetcher {
  return async (input, init) => {
    const token = getToken();
    const headers = new Headers(init?.headers);
    if (token) headers.set('Authorization', `Bearer ${token}`);
    const res = await fetch(input, { ...init, headers });
    if (res.status === 401) {
      clearToken();
      window.dispatchEvent(new Event('fy:auth-expired'));
    }
    return res;
  };
}
```

The SDK accepts `Fetcher` as the seam — anything that returns
`Promise<Response>` with the `(input, init)` shape works. No
monkey-patching of `window.fetch`, no per-client setup. The fetcher
runs outside the React tree, so it cannot read context; it surfaces
the 401 via a window event the hook listens for.

`src/App.tsx` gates on auth state and mounts the provider:

```tsx title="src/App.tsx"
const { token, tenantId, email, loading, login, logout } = useAuth();
if (loading) return <p>Loading…</p>;
if (!token || !tenantId) return <LoginScreen login={login} />;
return (
  <FastYokeProvider
    tenantId={tenantId}
    fetcher={makeAuthFetcher()}
    baseUrl={import.meta.env.VITE_FASTYOKE_API_URL}
  >
    <AuthedApp email={email} onLogout={logout} />
  </FastYokeProvider>
);
```

Every component under `<AuthedApp>` can now call `useFastYoke()` and
get the typed `schemas` / `jobs` / `entities` / `files` clients plus
the realtime socket.

## 4. Tour: entity CRUD with realtime refresh

`src/features/ContactsPage.tsx` mounts the entire CRUD flow. The
[CRUD UI with the SDK Hooks](/docs/recipes/sdk-crud-ui) recipe has the
full row + editor implementation; the scaffold ships an identical
copy. The load-bearing hook calls:

```tsx
const { data, loading, error } = useEntities('contact', { page, pageSize: 20 });
const { createEntity, loading: creating } = useCreateEntity();
const { updateEntity, loading: updating } = useUpdateEntity();
const { deleteEntity, loading: deleting } = useDeleteEntity();
```

`useEntities` auto-refetches on every `entity_mutation` event whose
`entity_name === 'contact'`, including mutations from other browser
tabs and the admin shell. After a `createEntity` / `updateEntity` /
`deleteEntity` resolves, the realtime broadcast triggers the refetch
automatically — you never call `refetch()` yourself.

## 5. Tour: file upload (the raw-fetcher pattern)

The SDK's `FilesClient` exposes `downloadBlob` but no `upload` helper.
That is a deliberate design choice — uploads vary too much (multipart,
chunked, signed-URL) to settle on one interface. The scaffold posts
multipart directly through the same `Fetcher` the SDK uses, which
keeps the auth path consistent:

```tsx title="src/features/FilesPanel.tsx (excerpt)"
const { fetcher, files, tenantId } = useFastYoke();

async function onPick(e: ChangeEvent<HTMLInputElement>) {
  const file = e.target.files?.[0];
  if (!file) return;
  const form = new FormData();
  form.append('file', file);
  const apiUrl = import.meta.env.VITE_FASTYOKE_API_URL;
  const res = await fetcher(
    `${apiUrl}/api/v1/files?tenant_id=${encodeURIComponent(tenantId)}`,
    { method: 'POST', body: form },
  );
  if (!res.ok) throw new Error(`Upload failed (${res.status})`);
  const ref = await res.json();
  // ref: { __type: 'file_ref', file_id, filename, mime_type, size_bytes, scan_status }
}
```

This is the general pattern for any endpoint the typed client does
not wrap: ask `useFastYoke()` for the `fetcher` and hand-build the
request. You get the operator JWT, 401 handling, and CORS for free.

Downloads use the typed client — `files.downloadBlob(fileId)` returns
a `Blob` you turn into a browser download via `URL.createObjectURL` +
a synthetic `<a download>` click. The scaffold's `FilesPanel` shows
the full pattern, including `URL.revokeObjectURL` cleanup.

## 6. Tour: realtime jobs viewer

`src/features/JobsViewer.tsx` is a single hook call:

```tsx
const { data, loading, error } = useJobs({});
```

`useJobs` is realtime-aware out of the box — every FSM transition
the operator's account can see refetches the list. No subscription
plumbing, no `useEffect`, no manual filter. The scaffold renders a
plain HTML table; replace with whatever component library you prefer.

## 7. CORS: tell your FastYoke deployment about your Pages origin

The FastYoke API rejects cross-origin requests from any origin that is
not on the `CORS_ALLOWED_ORIGINS` allowlist. For local development
that origin is `http://localhost:5173`; for the deployed SPA it is
your Pages domain (e.g. `https://my-portal.pages.dev` or a custom
domain).

::callout{type="info" title="Self-hosted"}
If you operate the FastYoke deployment, set the env var and redeploy:

```bash
fly secrets set CORS_ALLOWED_ORIGINS=https://my-portal.pages.dev,http://localhost:5173
```

Comma-separated origins, no spaces. The list is matched verbatim — no
wildcards.
::

::callout{type="info" title="Hosted (FastYoke Cloud)"}
Ask your platform admin to add your Pages origin to the deployment's
allowlist. Until that lands, the browser DevTools console will show
the CORS rejection on every API call.
::

## 8. Deploy to Cloudflare Pages via GitHub

1. **Push the project to GitHub.** A new private repo is fine.
2. **Edit `public/_headers`** to extend `connect-src` with your
   FastYoke origin — both `https://` for REST and `wss://` for
   realtime go in the same directive. Example:

   ```text title="public/_headers (edited)"
   /*
     Content-Security-Policy: default-src 'self'; connect-src 'self' https://app.fastyoke.io wss://app.fastyoke.io; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; font-src 'self' data:;
   ```

   Commit and push.
3. **Connect the repo in Cloudflare.** In the Cloudflare dashboard:
   **Workers & Pages → Create → Pages → Connect to Git**. Pick the
   repo. Framework preset: **Vite**. The build command and output
   directory fill in automatically (`npm run build` and `dist`).
4. **Add the build environment variable** `VITE_FASTYOKE_API_URL`
   pointing at your FastYoke deployment (production value — not
   `localhost`). Set it for both the Production and Preview
   environments.
5. **Deploy.** Cloudflare runs `npm install && npm run build` and
   serves `dist/`. Subsequent pushes auto-deploy.

::callout{type="warn" title="Forgot the CORS step?"}
The deployed SPA renders fine but every API call fails with a CORS
error in DevTools. Re-check step 7 — the deployed origin needs to be
allowlisted, separately from your localhost origin.
::

## 9. Verify it works

Pass criteria for a successful deploy:

- [ ] You can sign in with operator credentials at the deployed URL.
- [ ] The Contacts tab loads existing records.
- [ ] Creating a contact in the SPA appears in the admin shell at
  `/admin/entities/contact` immediately.
- [ ] Editing a contact in the admin shell appears in the SPA without
  a manual refresh (realtime is working).
- [ ] Uploading a file shows a `scan: clean` line; downloading it
  returns the same bytes.
- [ ] The Jobs tab lists existing jobs; firing a transition elsewhere
  refreshes the list automatically.
- [ ] A hard browser refresh keeps you signed in (token rehydration
  via `/auth/me`).
- [ ] Signing out clears localStorage and returns you to the login
  screen.

## What's next

- [SDK reference](/docs/sdk/reference) — every hook + every client method.
- [CRUD UI with the SDK Hooks](/docs/recipes/sdk-crud-ui) — alternative
  patterns for the entity CRUD case.
- [Extension authoring](/docs/recipes/extension-authoring) — when you
  want to ship the same code as an in-shell extension instead of (or
  alongside) the standalone SPA.
