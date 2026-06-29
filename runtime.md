---
title: FastYoke Runtime — free for single-node and development
summary: The full FastYoke FSM engine, free forever for local development and single-node deployments. No cloud account. No internet. Same engine as production — what you build here deploys unchanged.
order: 6
---

::hero{eyebrow="Post-GA · Free forever · No cloud account" title="The FastYoke engine, running on your machine" tagline="FastYoke Runtime is the full FSM engine for local development and single-node deployments. The deployment platform ships at general availability — join the waitlist on /download until then. No internet connection required for local work. What you build here deploys to Cloud SaaS or On-Prem unchanged — the engine is identical."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

::maturity-badge{status="post-ga"}

## Three minutes to a running app

```bash
npx fastyoke init    # scaffold .fastyoke/ + seed.json
npx fastyoke dev     # boot the local sidecar on :3001
```

Open your browser. Your entities are there, your FSM transitions
fire, your forms collect submissions — all in a SQLite file on
your own disk. No account. No API key. No outbound traffic.

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Who it's for

#title
1. Three developer profiles

#lede
The Runtime tier is free for all three of these cases. You don't need to pick one — the same binary handles all of them.

#default
<div id="profiles"></div>

::feature-grid{columns="3"}
  ::feature-card{title="Evaluating FastYoke" icon="crm" accent="cyan" summary="You want to understand what the FSM engine can do before signing up for Cloud SaaS. Run the full engine locally, build a real app, and make the decision with data — not a sandbox with artificial limits."}
  ::
  ::feature-card{title="ISV building an embedded app" icon="warehouse" accent="amber" summary="Your product ships inside a customer's firewall. FastYoke Runtime is the right local dev and CI target. The same app package that runs locally deploys to the customer's On-Prem install without modification."}
  ::
  ::feature-card{title="Consultant prototyping" icon="logistics" accent="violet" summary="You're building a client demo or a vertical-specific app for a sales conversation. Runtime boots in three minutes on your laptop, runs offline during the pitch, and hands off to a Cloud SaaS tenant when the client is ready to go live."}
  ::
::

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
What's included

#title
2. Every engine primitive, free

#default
The Runtime tier does not gate features — it gates topology. The
engine you get is identical to Cloud SaaS and On-Prem:

- **Finite State Machine engine.** Multi-board FSMs with guards,
  self-loop transitions, and the admin cancel override. The same
  JSON-logic predicates that run in production evaluate locally.
- **Forms + PDF rendering.** Public forms collect submissions into
  the local SQLite database. PDF export (typst-based) renders on
  your machine with no cloud call.
- **WASM scripting tier.** TypeScript on QuickJS in wasmtime.
  The same fuel + memory caps apply — behavior is deterministic
  across Runtime and Cloud SaaS.
- **Typed SDK.** `@fastyoke/sdk-local` is the Runtime sidecar
  client. It is API-compatible with `@fastyoke/sdk-next` (Cloud
  SaaS) — swap the import, swap the env var, the same application
  code runs.
- **Event log.** The append-only event log writes locally. Every
  FSM transition is recorded. The audit trail survives a restart.
- **Marketplace app schemas.** CRM Suite, Yoke Ledger, Inventory,
  Field Service — all the app schemas are available locally. You
  can seed a full multi-app tenant and develop against it offline.

What changes at the topology boundary is replication, HA, and
WebSocket fanout to multiple nodes. Those require Cloud SaaS or
On-Prem.

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
The upgrade path

#title
3. Runtime → Cloud SaaS is one config swap

#default
This is the point of the Runtime tier: you build once and it
runs everywhere.

The only difference between a Runtime app and a Cloud SaaS app
is the SDK client import and the environment variable that points
at the API:

```ts
// Runtime (local sidecar)
import { createClient } from '@fastyoke/sdk-local'
const client = createClient({ baseUrl: 'http://localhost:3001' })

// Cloud SaaS (same app, new import)
import { createClient } from '@fastyoke/sdk-next'
const client = createClient({ apiKey: process.env.FASTYOKE_API_KEY })
```

The FSM schemas, entity definitions, form configs, and WASM scripts
are identical. The migrations directory is identical. The app
package you export from the Runtime Studio installs on a Cloud SaaS
tenant or an On-Prem deployment without modification.

There is no "Runtime version" of the schema and a "Cloud version."
There is one schema.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
4. What Runtime isn't for

#default
- **Not a production multi-user server.** Single-node means no
  write-ahead log replication, no failover, and no WebSocket
  fanout to multiple app servers. For production workloads with
  more than one operator or any HA requirement, use Cloud SaaS
  or On-Prem.
- **No marketplace app auto-update.** Cloud SaaS tenants receive
  marketplace app updates automatically. Runtime operators pull
  updates via `npx fastyoke upgrade`.
- **No managed backups.** The SQLite file is yours — back it up
  yourself. Cloud SaaS and On-Prem include operator-managed backup
  pipelines (Litestream to S3-compatible storage).
- **No zero-downtime deploys.** Upgrading the Runtime binary
  requires a restart. The event log and tenant database survive
  the restart; in-flight HTTP requests do not.
- **Single tenant per sidecar.** The Runtime sidecar serves one
  tenant at a time. Multi-tenant development (e.g. simulating two
  isolated customer tenants) requires two sidecar processes on
  different ports.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

## Get started

```bash
# Requires Node 18+ for the CLI runner
npx fastyoke init
npx fastyoke dev
```

The CLI bootstraps a `.fastyoke/` directory with a `local.db`
SQLite file, a `seed.json` you can edit, and a `token` file your
frontend reads for authenticated API calls.

::cta-button{to="/docs/getting-started" variant="primary" size="lg"}
Read the getting-started guide
::

::cta-button{to="/developers" variant="secondary" size="lg"}
FastYoke for developers & ISVs
::

For ISVs embedding Runtime inside a client's firewall, see the
[On-Prem deployment guide](/on-prem) — the same single binary
runs in both modes.

::

::marketing-section{band="white" maxWidth="4xl"}
#default
## Looking for more control?

Runtime is the fastest way to start — but FastYoke is designed to run anywhere your data lives.

<div class="mt-6 grid grid-cols-1 sm:grid-cols-2 gap-4">
  <div class="rounded-xl border p-5 flex flex-col gap-2" style="border-color: var(--brand-border-subtle); background: var(--brand-bg-surface)">
    <div class="flex items-center justify-center shrink-0" style="width: 36px; height: 36px; border-radius: 8px; background: rgba(148, 163, 184, 0.14); color: #94a3b8" aria-hidden="true"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><rect x="5" y="5" width="14" height="6" rx="1"/><circle cx="8" cy="8" r="1"/><rect x="5" y="13" width="14" height="6" rx="1"/><circle cx="8" cy="16" r="1"/></svg></div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">On-Prem</h3>
    <p class="text-sm text-[var(--brand-text-secondary)] flex-1">Deploy the same single binary on your own servers. Air-gap friendly, no outbound calls, full data sovereignty. Preferred by healthcare and financial services customers.</p>
    <a href="/on-prem" class="mt-2 text-sm font-medium text-[var(--brand-primary)]">On-Prem deployment →</a>
  </div>
  <div class="rounded-xl border p-5 flex flex-col gap-2" style="border-color: var(--brand-border-subtle); background: var(--brand-bg-surface)">
    <div class="flex items-center justify-center shrink-0" style="width: 36px; height: 36px; border-radius: 8px; background: rgba(129, 140, 248, 0.14); color: #818cf8" aria-hidden="true"><svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><rect x="8" y="3" width="8" height="18" rx="2"/><path d="M11 18h2"/><path d="M16 7a2 2 0 0 1 0 3"/></svg></div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">Substrate <span style="color: var(--brand-primary)">✦</span></h3>
    <p class="text-sm text-[var(--brand-text-secondary)] flex-1">Extend FastYoke to native Android + iOS for your employee fleet. OTA bundle delivery, offline-capable, composable with Runtime or On-Prem. Team+ add-on, early access Q1 2027.</p>
    <a href="/substrate" class="mt-2 text-sm font-medium text-[var(--brand-primary)]">Substrate for mobile fleets →</a>
  </div>
</div>
::
