# FastYoke — The Open-Architecture aPaaS

> Build at the speed of low-code, perform at the speed of the edge, leave whenever you want. Multi-tenant FSM platform for ISVs who refuse vendor lock-in.

<Hero
  eyebrow={<span style={{color: 'var(--brand-primary)'}}>FASTYOKE</span>}
  title={<>Build at the speed of <em>Low-Code</em>.<br/>Perform at the speed of the <em>Edge</em>.<br/>Leave whenever you want.</>}
  subtitle="The open-architecture App Platform as a Service designed for zero vendor lock-in."
  actions={
    <>
      
      
        Read the docs
      
    </>
  }
/>

<section className="mx-auto max-w-3xl px-4 pb-12 text-center">
  <p className="text-base leading-relaxed text-[var(--brand-text-secondary)]">
    Traditional Low-Code Application Platforms force a brutal compromise: speed to market in exchange for proprietary vendor lock-in. <strong className="text-[var(--brand-text-primary)]">FastYoke shatters this paradigm.</strong> We empower Independent Software Vendors to visually generate enterprise-grade applications that compile down to standard, ejectable codebases.
  </p>
</section>

<section className="mx-auto max-w-6xl px-4 pb-4 text-center">
  <p className="text-xs font-medium uppercase tracking-wider text-[var(--brand-text-secondary)]">
    The Sovereignty Triad
  </p>
  <h2 className="mt-3 text-3xl font-semibold tracking-tight text-[var(--brand-text-primary)]">
    Three guarantees you won't get from a typical LCAP
  </h2>
</section>

  <FeatureCard
    title={<><span className="mr-2 text-[var(--brand-primary)]">01.</span>UI/UX Sovereignty <span className="text-[var(--brand-text-secondary)]">(Next.js &amp; Astro)</span></>}
    description={<>Stop compromising your user experience with generic, drag-and-drop dashboards. FastYoke's Advanced App Builder generates standard Next.js and Astro applications. Deploy on <a href="/pricing#hosting" className="underline">Vercel via our marketplace</a>, or any host you already use — you own the frontend repository.</>}
  />
  <FeatureCard
    title={<><span className="mr-2 text-[var(--brand-primary)]">02.</span>Logic Sovereignty <span className="text-[var(--brand-text-secondary)]">(Rust / Wasm)</span></>}
    description="Ditch slow, proprietary scripting languages. FastYoke's execution layer is powered by a sub-microsecond Rust engine. Your core business logic is compiled to WebAssembly and rigidly enforced by a state machine. It runs anywhere, at edge speeds."
  />
  <FeatureCard
    title={<><span className="mr-2 text-[var(--brand-primary)]">03.</span>Data Sovereignty <span className="text-[var(--brand-text-secondary)]">(The SQLite Fleet)</span></>}
    description="Multi-tenant databases are a compliance nightmare. FastYoke uses a Fleet of Files architecture: every single tenant operates on their own physically isolated SQLite database, continuously synced via Litestream. Complete data isolation, portability, and auditability."
  />

<section className="mx-auto max-w-3xl px-4 pt-8">
  
> **The Ultimate Enterprise Parachute**
>
> De-risk your platform strategy today. Prototype and scale on FastYoke with absolute exit certainty. If you ever outgrow us, you take your Next.js frontend, your compiled Wasm rules, and your raw SQLite databases with you. Architect your next platform without the handcuffs.

</section>

<section className="mx-auto max-w-6xl px-4 pt-16 pb-2 text-center">
  <p className="text-xs font-medium uppercase tracking-wider text-[var(--brand-text-secondary)]">
    How it works
  </p>
  <h2 className="mt-3 text-2xl font-semibold tracking-tight text-[var(--brand-text-primary)]">
    The primitives behind the sovereignty
  </h2>
</section>

  
  
  <FeatureCard
    title="Typed SDK"
    description={<>The <code>@fastyoke/sdk</code> package ships React hooks, Zod schemas, and a realtime WebSocket client. Familiar ergonomics; zero boilerplate.</>}
  />
  

<section className="mx-auto max-w-3xl px-4 pt-20 pb-4 text-center">
  <p className="text-xs font-medium uppercase tracking-wider text-[var(--brand-text-secondary)]">
    Compliance, by construction
  </p>
  <h2 className="mt-3 text-2xl font-semibold tracking-tight text-[var(--brand-text-primary)]">
    WCAG 2.1 AA enforced in CI. VPAT-ready.
  </h2>
  <p className="mt-4 text-base leading-relaxed text-[var(--brand-text-secondary)]">
    Every PR runs Playwright + axe-core across every route in the
    admin SPA. Brand color saves are mathematically validated
    against WCAG 1.4.3 contrast thresholds — the platform offers
    a one-click compliant shade or refuses the save. ARIA live
    regions and a focus-trap modal primitive ship at the layout
    root. The procurement document writes itself.
  </p>
</section>

  
  
  

<CodeShowcase
  eyebrow="TypeScript"
  caption="Live workflow state in a dozen lines"
  footer={<>Full walkthrough in the <a href="/docs/sdk/reference">SDK reference</a> and the <a href="/docs/recipes/sdk-crud-ui">CRUD UI recipe</a>.</>}
>

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

<section className="mx-auto max-w-4xl px-4 pb-4 text-center">
  <p className="text-sm uppercase tracking-wider text-[var(--brand-text-secondary)]">
    Works anywhere fetch works
  </p>
  <p className="mt-3 text-base font-medium text-[var(--brand-text-primary)]">
    Next.js · Remix · Astro · your own Node server
  </p>
</section>

<section className="mt-8 bg-[var(--brand-bg-surface-raised)] py-20">
  <div className="mx-auto max-w-3xl px-4 text-center">
    <h2 className="text-3xl font-semibold tracking-tight text-[var(--brand-text-primary)]">
      One install. Full backend.
    </h2>
    <p className="mx-auto mt-4 max-w-xl text-[var(--brand-text-secondary)]">
      Provision an isolated FastYoke tenant straight from the Vercel dashboard — a dedicated database, a scoped API token, and env vars injected into your Next.js project in one OAuth handshake.
    </p>
    <div className="mt-8 flex flex-wrap items-center justify-center gap-3">
      
      
        See the install flow
      
    </div>
  </div>
</section>
