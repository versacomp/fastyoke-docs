---
title: FastYoke — The Open-Architecture aPaaS
summary: Build at the speed of low-code, perform at the speed of the edge, leave whenever you want. Multi-tenant FSM platform for ISVs who refuse vendor lock-in.
order: 1
---

::hero{eyebrow="FASTYOKE" tagline="The open-architecture App Platform as a Service designed for zero vendor lock-in." graphic="story"}
#title
Build at the speed of <em>Low-Code</em>.<br/>Perform at the speed of the <em>Edge</em>.<br/>Total architectural <em>freedom</em>.

#default
::signup-cta{size="lg" variant="primary" location="hero-home"}
::

::marketplace-cta{size="lg" variant="secondary" location="hero-home"}
::
::

::prose-container{max-width="3xl" padding="md"}

Traditional Low-Code Application Platforms force a brutal compromise: speed to market in exchange for proprietary vendor lock-in. **FastYoke shatters this paradigm.** We empower Independent Software Vendors to visually generate enterprise-grade applications that compile down to standard, ejectable codebases.

::

::prose-container{max-width="6xl" padding="lg"}

The Sovereignty Triad

## Three guarantees you won't get from a typical LCAP

::

::feature-grid{columns="3"}
  ::feature-card{title="01. UI/UX Sovereignty (Next.js & Astro)" summary="Stop compromising your user experience with generic, drag-and-drop dashboards. FastYoke's Advanced App Builder generates standard Next.js and Astro applications. Deploy on [Vercel via our marketplace](/pricing#hosting), or any host you already use — you own the frontend repository."}
  ::
  ::feature-card{title="02. Logic Sovereignty (Rust / Wasm)" summary="Ditch slow, proprietary scripting languages. FastYoke's execution layer is powered by a sub-microsecond Rust engine. Your core business logic is compiled to WebAssembly and rigidly enforced by a state machine. It runs anywhere, at edge speeds."}
  ::
  ::feature-card{title="03. Data Sovereignty (The SQLite Fleet)" summary="Multi-tenant databases are a compliance nightmare. FastYoke uses a Fleet of Files architecture: every single tenant operates on their own physically isolated SQLite database, continuously synced via Litestream. Complete data isolation, portability, and auditability."}
  ::
::

::callout{type="info" title="The Ultimate Enterprise Parachute"}
De-risk your platform strategy today. Prototype and scale on FastYoke with absolute exit certainty. If you ever outgrow us, you take your Next.js frontend, your compiled Wasm rules, and your raw SQLite databases with you. Architect your next platform without the handcuffs.
::

::prose-container{max-width="6xl" padding="lg"}

How it works

## The primitives behind the sovereignty

::

::feature-grid{columns="4"}
  ::feature-card{title="Sandboxed guards" summary="JSONLogic predicates by default, Wasm when you need compute — never raw eval. Fuel-capped, memory-bounded, audit-ready."}
  ::
  ::feature-card{title="Tenant-isolated" summary="Every query carries a WHERE tenant_id clause by construction. Per-tenant SQLite files keep data locality strict and portable."}
  ::
  ::feature-card{title="Typed SDK" summary="The `@fastyoke/sdk` package ships React hooks, Zod schemas, and a realtime WebSocket client. Familiar ergonomics; zero boilerplate."}
  ::
  ::feature-card{title="Ship extensions" summary="Pack a React SPA as a tenant-scoped bundle. Load at runtime behind a scanner + minted JWT — marketplace-ready by design."}
  ::
::

::prose-container{max-width="3xl" padding="lg"}

Compliance, by construction

## WCAG 2.1 AA enforced in CI. VPAT-ready.

Every PR runs Playwright + axe-core across every route in the
admin SPA. Brand color saves are mathematically validated
against WCAG 1.4.3 contrast thresholds — the platform offers
a one-click compliant shade or refuses the save. ARIA live
regions and a focus-trap modal primitive ship at the layout
root. The procurement document writes itself.

::

::feature-grid{columns="3"}
  ::feature-card{title="Axe-core CI gate" summary="WCAG 2.1 AA structural checks fail the build on critical or serious violations. Allowlist entries auto-expire after 90 days — there's no mute-and-forget path."}
  ::
  ::feature-card{title="Theme contrast on save" summary="Branding hex codes validated against 4.5:1 / 3:1 thresholds in real time. Auto-suggests the nearest compliant shade; explicit bypass writes an audit row to a tenant-scoped, append-only ledger."}
  ::
  ::feature-card{title="Focus + announcements" summary="Radix-based Modal primitive owns Tab cycling, Escape dismissal, and focus restoration. ARIA announcer at the admin shell root delivers state-change messages to screen readers without visible chrome."}
  ::
::

::code-showcase
#eyebrow
TypeScript
#caption
Live workflow state in a dozen lines

```tsx title="JobPage.tsx"
import { useJob, WorkflowHistory, useTransitionJob } from '@fastyoke/sdk';

export function JobPage({ id }: { id: string }) {
  const { data: job } = useJob(id);                 // auto-refetches on transitions
  const { transitionJob, loading } = useTransitionJob();

  return (
    <>
      <h1>{job?.current_state ?? 'Loading…'}</h1>
      <button
        disabled={loading}
        onClick={() => transitionJob({ id, input: { eventType: 'approve' } })}
      >
        Approve
      </button>
      <WorkflowHistory jobId={id} />                 // live, no polling
    </>
  );
}
```

#footer
Full walkthrough in the [SDK reference](/docs/sdk/reference) and the [CRUD UI recipe](/docs/recipes/sdk-crud-ui).
::

::prose-container{max-width="4xl" padding="lg"}

Works anywhere fetch works

**Next.js · Remix · Astro · your own Node server**

::

<section class="mt-8 bg-[var(--brand-bg-surface-raised)] py-20">
<div class="mx-auto max-w-3xl px-4 text-center">

## One install. Full backend.

Sign up direct and you get an isolated FastYoke tenant — dedicated database, scoped API token, the full SDK. Or install from the Vercel Marketplace to skip credential wiring entirely: your env vars get auto-injected into your Next.js project in one OAuth handshake.

<div class="mt-6 flex flex-wrap items-center justify-center gap-3">

::signup-cta{size="lg" variant="primary" location="home-bottom"}
::

::marketplace-cta{size="lg" variant="secondary" location="home-bottom"}
::

</div>

</div>
</section>
