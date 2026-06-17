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
::pricing-metered-matrix
::

::pricing-calculator
::
::

::marketing-section{band="gray" maxWidth="6xl"}
#default
::pricing-forms-matrix
::
::

::marketing-section{band="white" maxWidth="6xl"}
#default
::pricing-addons
::
::

::marketing-section{band="gray" maxWidth="6xl"}
#default
<h2 class="text-2xl font-semibold text-[var(--brand-text-primary)]">Compare to what you're paying now</h2>
<p class="mt-3 text-sm text-[var(--brand-text-secondary)]">
  The Team tier sits where most product teams land — five admin users,
  three tenants, the Advanced App Builder, and 1M transitions a month.
  Here's how the same workload prices on the platforms it most often
  replaces.
</p>

<div class="mt-6 overflow-x-auto">
  <table role="table" class="w-full min-w-[640px] border-collapse text-sm">
    <thead>
      <tr class="border-b" style="border-color: var(--brand-sidebar-border)">
        <th class="px-3 py-3 text-left font-medium" style="color: var(--brand-text-secondary)">Scenario</th>
        <th class="px-3 py-3 text-left font-medium" style="color: var(--brand-text-secondary)">Typical alternative</th>
        <th class="px-3 py-3 text-left font-medium" style="color: var(--brand-text-secondary)">FastYoke&trade; Team</th>
        <th class="px-3 py-3 text-left font-medium" style="color: var(--brand-text-secondary)">You save</th>
      </tr>
    </thead>
    <tbody>
      <tr class="border-t" style="border-color: var(--brand-sidebar-border)">
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">5-dev product team on an internal-tools builder</td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">Retool Business — $50/user × 5 = <strong>$250/mo</strong></td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)"><strong>$180/mo</strong></td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">~28%</td>
      </tr>
      <tr class="border-t" style="border-color: var(--brand-sidebar-border)">
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">Mid-volume SaaS on a managed backend</td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">Supabase Team — <strong>~$599/mo</strong> (Team seat + typical usage)</td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)"><strong>$180/mo</strong></td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">~70%</td>
      </tr>
      <tr class="border-t" style="border-color: var(--brand-sidebar-border)">
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">No-code app team on a hosted builder</td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">Bubble Team — <strong>$399/mo</strong></td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)"><strong>$180/mo</strong></td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">~55%</td>
      </tr>
      <tr class="border-t" style="border-color: var(--brand-sidebar-border)">
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">DIY stack — managed Postgres + queue + auth + edge host</td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)"><strong>$400–600/mo</strong> across 4 vendors</td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)"><strong>$180/mo</strong> (one bill)</td>
        <td class="px-3 py-3" style="color: var(--brand-text-primary)">55%+</td>
      </tr>
    </tbody>
  </table>
</div>

<p class="mt-4 text-xs italic text-[var(--brand-text-secondary)]">
  Competitor prices are public list rates as of June 2026. Numbers exclude
  the engineering hours you'd spend wiring four vendors together —
  FastYoke is a single binary with the FSM engine, tenant isolation,
  realtime, and the SDK already wired.
</p>
::

::marketing-section{band="white" maxWidth="3xl"}
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

::marketing-section{band="gray" maxWidth="3xl"}
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
