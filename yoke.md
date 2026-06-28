---
title: Yoke — AI operations assistant for field service
summary: Yoke proposes concrete improvements to your workflows from patterns in your event log. Every change is a brief you approve. Weekly digests, No-Show ML, and Time Travel connect recommendations to measured outcomes.
order: 4
---

::hero{eyebrow="Yoke Pro · Field service" title="Yoke proposes. You approve." tagline="Yoke is FastYoke's operations assistant for dispatch-heavy businesses. It reads your jobs, transitions, and customer history — then surfaces **briefs**: persisted recommendations with evidence, predicted impact, and an apply preview. Nothing changes until you say so."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## The product promise

*FastYoke makes your business better while you sleep — but only after you approve the plan.*

Yoke is personified in copy (*"Yoke noticed…"*, *"Yoke recommends…"*) and never edits your schemas autonomously. Every brief is a durable row in your tenant database — not transient chat output.

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
The inbox

#title
1. Briefs are first-class data

#lede
Each brief carries a plain-English claim, chart-ready evidence, a proposed action, a confidence tier, and a conservatively discounted impact estimate.

#default
::feature-grid{columns="3"}
  ::feature-card{title="Claim + evidence" icon="crm" accent="rose" summary="Every brief shows what Yoke noticed and the numbers behind it — top cohorts, SLA breaches, bottleneck states — not a vague AI summary."}
  ::
  ::feature-card{title="Apply preview" icon="field-service" accent="sky" summary="Before you approve, see what changes: workflow edges, form fields, notification rules. Replay against recent jobs where available."}
  ::
  ::feature-card{title="Time Travel linkage" icon="project-tracker" accent="violet" summary="Applied briefs write schema commits with back-pointers. History reads cause-and-effect: approve on Tuesday, measure on Wednesday."}
  ::
::
::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
The digest

#title
2. Yoke digest — weekly ops in your inbox

#lede
Distinct from **report digests** (scheduled dashboard KPI emails). The Yoke digest bundles pending briefs worth a look — with magic links back to your inbox.

#default
::feature-grid{columns="2"}
  ::feature-card{title="Digest mode" icon="forms" accent="cyan" summary="Most briefs arrive in the weekly digest: bottleneck detection, SLA risk, invoice latency patterns. Scan on mobile, approve at your desk."}
  ::
  ::feature-card{title="Alert mode" icon="logistics" accent="amber" summary="Urgent briefs can break out as immediate alerts when a template marks high urgency — still gated by your approval."}
  ::
::

::callout{type="note"}
**Naming:** *Yoke digest* = ops recommendations. *Report digest* = dashboard aggregates. See [scheduled report digests](/docs/reports/digests) for KPI email scheduling.
::

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Machine learning

#title
3. No-Show ML

#lede
<div id="no-show"></div>
The only ML-backed Yoke template in the first wave: logistic regression trained on **your** completed and no-show jobs — scored on your VM, surfaced as a single brief listing at-risk customers on upcoming bookings.

#default
::feature-grid{columns="2"}
  ::feature-card{title="Your history, your model" icon="field-service" accent="amber" summary="Features include prior no-show count, booking lead time, and first-visit flag. Training requires ~200 historical jobs; the model retrains on a weekly cadence."}
  ::
  ::feature-card{title="Honest cold start" icon="warehouse" accent="rose" summary="New tenants connect ServiceTitan or Jobber to backfill event history — tomorrow's digest can reference six months of imported jobs without a big-bang migration."}
  ::
::

::cta-button{to="/solutions/field-service" variant="secondary" size="sm"}
Field service vertical
::
::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Connectors

#title
4. Cold start without a migration

#default

Yoke's marquee briefs need weeks of `event_log` to fire. V1 ships ingestion connectors that backfill from systems you already run:

- **ServiceTitan** — historical jobs and customers synthesized into entity records and event log rows.
- **Jobber** — same shape for smaller shops already on Jobber dispatch.

The pitch: *Connect yesterday. Read the digest tomorrow.*

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Pricing

#title
5. Yoke Pro

#default

Yoke Pro is a paid tier on top of FastYoke base — launched against field service at the 5–50 employee band. A 90-day auto-trial aligns with the impact-tracker window so renewal conversations show **measured** savings, not predicted ones.

::cta-button{to="mailto:sales@fastyoke.io?subject=Yoke%20Pro%20early%20access" variant="primary" size="lg"}
Talk to sales about Yoke Pro
::

::cta-button{to="/ai" variant="secondary" size="md"}
All AI capabilities
::
::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
Related

#title
Build lane · Ask lane

#default
<div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
  <div class="rounded-xl border p-5" style="border-color: var(--brand-border-subtle)">
    <h3 class="font-semibold text-[var(--brand-text-primary)]">Yoke Architect</h3>
    <p class="text-sm text-[var(--brand-text-secondary)] mt-1">Build multi-board apps from a one-sentence brief — same validator, human edit before save.</p>
    <a href="/yoke-architect" class="text-sm font-medium text-[var(--brand-primary)] mt-2 inline-block">Read the thesis →</a>
  </div>
  <div class="rounded-xl border p-5" style="border-color: var(--brand-border-subtle)">
    <h3 class="font-semibold text-[var(--brand-text-primary)]">Yoker</h3>
    <p class="text-sm text-[var(--brand-text-secondary)] mt-1">Ask questions about entity records — retrieval embeddings run on your VM.</p>
    <a href="/yoker" class="text-sm font-medium text-[var(--brand-primary)] mt-2 inline-block">Yoker overview →</a>
  </div>
</div>
::
