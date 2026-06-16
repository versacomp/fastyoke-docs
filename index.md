---
title: FastYoke — The Open-Architecture aPaaS
summary: Build at the speed of low-code, perform at the speed of the edge, leave whenever you want. Multi-tenant FSM platform for ISVs who refuse vendor lock-in.
order: 1
---

::hero-carousel
#story-a-eyebrow
FASTYOKE

#story-a-title
Build at the speed of <em>Low-Code</em>.<br/>Perform at the speed of the <em>Edge</em>.<br/>Total architectural <em>freedom</em>.

#story-a-tagline
The open-architecture App Platform as a Service designed for zero vendor lock-in.

#story-a-cta
::signup-cta{size="lg" variant="primary" location="hero-home-platform"}
::

::marketplace-cta{size="lg" variant="secondary" location="hero-home-platform"}
::

#story-b-eyebrow
SOLO · FREE FOREVER

#story-b-title
Build forms <em>for free</em>. No vendor lock-in.

#story-b-tagline
The same form builder our enterprise customers ship to production — free for personal projects and small businesses.

#story-b-cta
::signup-cta{size="lg" variant="primary" location="hero-home-forms"}
Build a form free
::

::cta-button{to="/free-forms" variant="secondary" size="lg"}
See the features
::

#story-c-eyebrow
STUDIO · COMING SOON

#story-c-title
Build an app, not a <em>config</em>.

#story-c-tagline
Compose real software from finite-state boards — generate it, version it, own it. Studio is how you build an app on FastYoke.

#story-c-cta
::cta-button{to="/studio" variant="primary" size="lg"}
Explore Studio
::
::

::marketing-section{band="white" maxWidth="3xl"}
Traditional Low-Code Application Platforms force a brutal compromise: speed to market in exchange for proprietary vendor lock-in. **FastYoke&trade; shatters this paradigm.** We empower Independent Software Vendors to visually generate enterprise-grade applications that compile down to standard, ejectable codebases.
::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
The Sovereignty Triad

#title
Three guarantees you won't get from a typical LCAP

#default
::feature-grid{columns="3"}
  ::feature-card{title="01. UI/UX Sovereignty (Next.js & Astro)" summary="Stop compromising your user experience with generic, drag-and-drop dashboards. FastYoke's Advanced App Builder generates standard Next.js and Astro applications. Deploy on [Vercel via our marketplace](/pricing#hosting), or any host you already use — you own the frontend repository."}
  ::
  ::feature-card{title="02. Logic Sovereignty (Rust / Wasm)" summary="Ditch slow, proprietary scripting languages. FastYoke's execution layer is powered by a sub-microsecond Rust engine. Your core business logic is compiled to WebAssembly and rigidly enforced by a state machine. It runs anywhere, at edge speeds."}
  ::
  ::feature-card{title="03. Data Sovereignty (The SQLite Fleet)" summary="Multi-tenant databases are a compliance nightmare. FastYoke uses a Fleet of Files architecture: every single tenant operates on their own physically isolated SQLite database, continuously synced via Litestream. Complete data isolation, portability, and auditability."}
  ::
::
::

::marketing-section{band="gray" maxWidth="3xl"}
::callout{type="info" title="The Ultimate Enterprise Parachute"}
De-risk your platform strategy today. Prototype and scale on FastYoke with absolute exit certainty. If you ever outgrow us, you take your Next.js frontend, your compiled Wasm rules, and your raw SQLite databases with you. Architect your next platform without the handcuffs.
::
::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
How it works

#title
The primitives behind the sovereignty

#default
::two-col-row
#title
Sandboxed guards

JSONLogic predicates by default, Wasm when you need compute — never raw eval. Fuel-capped, memory-bounded, audit-ready.

#media
::feature-card{title="Tenant-isolated" summary="Every query carries a WHERE tenant_id clause by construction. Per-tenant SQLite files keep data locality strict and portable."}
::
::

::two-col-row{media="left"}
#title
Typed SDK

The `@fastyoke/sdk` package ships React hooks, Zod schemas, and a realtime WebSocket client. Familiar ergonomics; zero boilerplate.

#media
::feature-card{title="Ship extensions" summary="Pack a React SPA as a tenant-scoped bundle. Load at runtime behind a scanner + minted JWT — marketplace-ready by design."}
::
::
::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Compliance, by construction

#title
WCAG 2.1 AA enforced in CI. VPAT-ready.

#lede
Every PR runs Playwright + axe-core across every route in the admin SPA. Brand color saves are mathematically validated against WCAG 1.4.3 contrast thresholds — the platform offers a one-click compliant shade or refuses the save. ARIA live regions and a focus-trap modal primitive ship at the layout root. The procurement document writes itself.

#default
::feature-grid{columns="3"}
  ::feature-card{title="Axe-core CI gate" summary="WCAG 2.1 AA structural checks fail the build on critical or serious violations. Allowlist entries auto-expire after 90 days — there's no mute-and-forget path."}
  ::
  ::feature-card{title="Theme contrast on save" summary="Branding hex codes validated against 4.5:1 / 3:1 thresholds in real time. Auto-suggests the nearest compliant shade; explicit bypass writes an audit row to a tenant-scoped, append-only ledger."}
  ::
  ::feature-card{title="Focus + announcements" summary="Radix-based Modal primitive owns Tab cycling, Escape dismissal, and focus restoration. ARIA announcer at the admin shell root delivers state-change messages to screen readers without visible chrome."}
  ::
::
::

::marketing-section{band="white" maxWidth="6xl"}
::code-showcase
#eyebrow
TypeScript
#caption
Live workflow state in a dozen lines

#default
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
::

::marketing-section{band="gray" maxWidth="3xl"}
Works anywhere fetch works

**Next.js · Remix · Astro · your own Node server**
::

::marketing-section{band="white" maxWidth="6xl"}
::customer-logo-strip{eyebrow="Built by teams shipping production aPaaS."}
::
::

::marketing-section{band="gray" maxWidth="3xl"}
#title
One install. Full backend.

Sign up direct and you get an isolated FastYoke tenant — dedicated database, scoped API token, the full SDK. Or install from the Vercel Marketplace to skip credential wiring entirely: your env vars get auto-injected into your Next.js project in one OAuth handshake.

::signup-cta{size="lg" variant="primary" location="home-bottom"}
::

::marketplace-cta{size="lg" variant="secondary" location="home-bottom"}
::
::
