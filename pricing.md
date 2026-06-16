---
title: Pricing — FastYoke
summary: Simple pricing that scales from a side project to a regulated fleet. Free with every Vercel Marketplace install; paid tiers on request.
order: 2
---

::hero{eyebrow="Pricing" title="Simple pricing that scales with you" tagline="Install free from the Vercel Marketplace and grow into the tier you need. No seat counts on the starter tier, no per-event gating on the core FSM."}
::

::marketing-section{band="gray" maxWidth="6xl"}
#default
::pricing-table
::
::

::marketing-section{band="white" maxWidth="6xl"}
#default
::pricing-forms-matrix
::
::

::marketing-section{band="gray" maxWidth="3xl"}
#default
<h2 class="text-2xl font-semibold text-[var(--brand-text-primary)]">Frequently asked</h2>

<div class="mt-8 grid gap-8 text-left sm:grid-cols-2">
  <div id="hobby">
    <h3 class="font-semibold text-[var(--brand-text-primary)]">What's included in Solo?</h3>
    <p class="mt-2 text-sm text-[var(--brand-text-primary)]">
      Every runtime primitive that matters — the FSM engine, tenant
      isolation, the full SDK, extensions, realtime. You can ship a
      real product on Solo and upgrade only when you hit a
      platform-scale feature gate.
    </p>
  </div>
  <div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">How do upgrades work?</h3>
    <p class="mt-2 text-sm text-[var(--brand-text-primary)]">
      Vercel handles the install; FastYoke&trade; handles the upgrade.
      Pro, Team, and Enterprise / ISV route through our billing
      surface — Solo and Pro are hard-capped on transition
      volume, Team can opt in to metered overages, Enterprise / ISV
      bills overages by default. Reach out to <a href="mailto:sales@fastyoke.io">sales@fastyoke.io</a> to provision.
    </p>
  </div>
  <div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">What's the Team tier for?</h3>
    <p class="mt-2 text-sm text-[var(--brand-text-primary)]">
      Five admin users, 1M transitions, and the Advanced App
      Builder — the AI Designer plus the multi-page extension
      generator that was previously Enterprise / ISV only. Team is the
      sweet spot for product teams that have outgrown solo Pro
      but don't need region pinning or HIPAA posture yet.
    </p>
  </div>
  <div id="hosting">
    <h3 class="font-semibold text-[var(--brand-text-primary)]">Where do I host my Next.js or Nuxt app?</h3>
    <p class="mt-2 text-sm text-[var(--brand-text-primary)]">
      FastYoke is the <strong>backend</strong>: FSM engine, tenant
      SQLite, typed SDK. Your <strong>frontend</strong> hosts on
      Vercel via our one-click <a href="/" class="underline">marketplace install</a>,
      or anywhere else you'd run a Next.js / Nuxt / Astro app
      (Cloudflare, Netlify, Render, Fly.io, your own infra). The
      Vercel path is fastest because it auto-injects FastYoke
      credentials into every environment; the others work the
      same way with manual env-var setup. Static-only SPAs
      (Next.js export, Nuxt generate, Astro static) can ship
      directly to FastYoke at the Enterprise / ISV tier — see
      the <a href="/docs/apps" class="underline">Apps guide</a> for
      the upload flow.
    </p>
  </div>
  <div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">Can I self-host?</h3>
    <p class="mt-2 text-sm text-[var(--brand-text-primary)]">
      FastYoke is a single-binary Rust monolith with a persistent
      volume — it runs anywhere Fly.io does, and anywhere Fly runs
      will run your own hardware. Self-hosting is a Fleet-tier
      option; talk to us for a dedicated contract.
    </p>
  </div>
  <div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">Is there a trial on paid tiers?</h3>
    <p class="mt-2 text-sm text-[var(--brand-text-primary)]">
      Every install starts on Solo for free. Paid-tier features
      require an active subscription — there is no separate trial,
      since Solo itself is the evaluation path.
    </p>
  </div>
  <div>
    <h3 class="font-semibold text-[var(--brand-text-primary)]">What about accessibility / VPAT?</h3>
    <p class="mt-2 text-sm text-[var(--brand-text-primary)]">
      WCAG 2.1 AA is a platform invariant on every tier — axe-core
      runs in CI on every PR, brand color saves are validated
      against the contrast formula in real time, and the focus-
      trap modal primitive ships at the layout root. Enterprise / ISV
      buyers get the generated VPAT 2.5 alongside the codebase
      for procurement review. See the <a href="/docs/accessibility" class="underline">accessibility docs</a> for the architectural contract.
    </p>
  </div>
</div>
::

::marketing-section{band="white" maxWidth="3xl"}
#default
<h2 class="text-2xl font-semibold tracking-tight text-[var(--brand-text-primary)]">Start on Solo. Upgrade when you need it.</h2>
<div class="mt-6 flex flex-wrap items-center justify-center gap-3">

::signup-cta{size="lg" variant="primary" location="pricing-bottom"}
::

::marketplace-cta{size="lg" variant="secondary" location="pricing-bottom"}
::

::cta-button{to="mailto:sales@fastyoke.io?subject=FastYoke%20pricing%20inquiry" variant="ghost" size="lg"}
Talk to sales
::

</div>
::
