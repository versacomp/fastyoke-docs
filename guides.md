---
title: Guides — FastYoke
summary: Opinionated guides on when FastYoke is the right move — graduation stories, vertical playbooks, decision frameworks, and the architectural decisions behind the platform.
order: 1
---

::hero{eyebrow="Guides" title="Opinionated guides for the moments FastYoke fits" tagline="Less polished than a comparison. Less constrained than the docs. Use these when you're deciding whether the platform shape is right for the job in front of you."}
::

::marketing-section{band="white" maxWidth="4xl"}
#default
<div id="guides-overview"></div>

## What's a guide here

A guide is an opinion piece — not a feature comparison,
not a tutorial. It tells the story of a specific decision
shape: **when FastYoke is the right move, when it isn't,
and what the migration looks like if it is.**

If you want a side-by-side table against a specific
platform, see [Compare](/compare). If you want a
step-by-step build, see [Tutorials](/docs/tutorials).
This surface lives in between.

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Featured guide

#title
When you outgrow Make.com

#lede
Make.com is genuinely great at one thing — orchestrating SaaS calls. Three concrete signals your work has stopped being that thing, and what FastYoke gives you on the other side. The piece doesn't position Make.com as inferior; the shape is the shape.

#default
<div id="featured-guide"></div>

::feature-grid{columns="2"}
  ::feature-card{title="Read the guide" icon="forms" accent="cyan" summary="Three load-bearing signals (state in weird places, non-technical UI needs, graph turning into a state machine), plus an honest tradeoffs section."}
  ::cta-button{to="/guides/when-you-outgrow-make" variant="primary" size="sm"}
  When you outgrow Make.com →
  ::
  ::
  ::feature-card{title="Compare side-by-side" summary="Looking for the table-shaped comparison against a specific platform? Compare carries the Make.com / Zapier / Retool / Supabase / Lovable matrix in one place."}
  ::cta-button{to="/compare" variant="secondary" size="sm"}
  See /compare
  ::
  ::
::
::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
Graduation stories

#title
When you outgrow the tool you started with

#lede
You picked a tool because it was the right shape for the work then. The work changed. Here's how to tell when that's the case — and what the migration looks like if it is.

#default
<div id="graduation-stories"></div>

::feature-grid{columns="3"}
  ::feature-card{title="When you outgrow Make.com" icon="logistics" accent="violet" summary="Three signals your workflow-automation tool has become a half-built application. State in weird places, non-technical UI needs, and the graph turning into a state machine."}
  ::cta-button{to="/guides/when-you-outgrow-make" variant="secondary" size="sm"}
  Read the guide →
  ::
  ::
  ::feature-card{title="When you outgrow Airtable" icon="inventory" accent="amber" summary="Your formulas have become business logic, your status field has become a contract, and your records have become workflow journeys. Three signals — and how to migrate one workflow at a time."}
  ::cta-button{to="/guides/when-you-outgrow-airtable" variant="secondary" size="sm"}
  Read the guide →
  ::
  ::
  ::feature-card{title="When you outgrow Zapier" icon="forms" accent="cyan" summary="Zaps chained into a stateful workflow, code-by-Zapier steps that became real scripts, and per-task billing for inherent N-step transactions. Three signals SaaS glue stopped being SaaS glue."}
  ::cta-button{to="/guides/when-you-outgrow-zapier" variant="secondary" size="sm"}
  Read the guide →
  ::
  ::
::
::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Vertical playbooks

#title
What this actually looks like in your industry

#lede
The shape of a deployment is more useful than the marketing taxonomy. These guides walk through the apps, the integrations, and the procurement story for one vertical at a time.

#default
<div id="vertical-playbooks"></div>

::feature-grid{columns="3"}
  ::feature-card{title="Hospital throughput on FastYoke" icon="crm" accent="rose" summary="Patient flow, transport, discharge planning, and EVS turnover — running on a per-tenant database with PHI encrypted at rest. The HL7v2 ADT connector keeps the boards live without a clinical-integration project."}
  ::cta-button{to="/guides/hospital-throughput-on-fastyoke" variant="secondary" size="sm"}
  Read the guide →
  ::
  ::
  ::feature-card{title="Multi-location HVAC on FastYoke" icon="field-service" accent="sky" summary="Dispatch boards, on-site state tracking, invoicing the moment the work is done, and the audit trail when a customer disputes a charge. Single-tenant-multi-facility vs one-tenant-per-location, and the realistic pilot path."}
  ::cta-button{to="/guides/multi-location-hvac-on-fastyoke" variant="secondary" size="sm"}
  Read the guide →
  ::
  ::
  ::feature-card{title="More vertical playbooks coming" summary="Multi-client 3PL warehouses. Auto-tech shop operations. Food-service ops paired with Pay n Go POS. These land alongside the early-access cohorts. Tell us your vertical at /getting-started."}
  ::cta-button{to="/getting-started" variant="secondary" size="sm"}
  Request early access
  ::
  ::
::
::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
Decision frameworks

#title
How to decide before you commit

#lede
The pieces that come up *before* you've picked a tier, an app, or a partner. Opinionated guides on the choices buyers make once and live with for years.

#default
<div id="decision-frameworks"></div>

::feature-grid{columns="2"}
  ::feature-card{title="Choosing a tier for your team size" icon="accounting" accent="emerald" summary="An opinionated walkthrough of the Solo / Starter / Team / Pro / Enterprise / Fleet ladder. Which tier fits your team today, which one fits in six months, and the two upgrade triggers nobody talks about."}
  ::cta-button{to="/guides/choosing-a-tier-for-your-team-size" variant="secondary" size="sm"}
  Read the guide →
  ::
  ::
  ::feature-card{title="More frameworks coming" summary="Build vs install vs extend on the marketplace. When the free tier is enough. When to bring in a Strategic Partner. Hosted vs ejected. Tell us which decision shape you're stuck on at /getting-started."}
  ::cta-button{to="/getting-started" variant="secondary" size="sm"}
  Request a framework
  ::
  ::
::
::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Architecture

#title
How FastYoke is built — and why that matters to you

#lede
The platform-level choices that shape every other claim. These pieces are for buyers and engineers who want to know what's holding up the marketing language.

#default
<div id="architecture"></div>

::feature-grid{columns="3"}
  ::feature-card{title="Why per-tenant database files" icon="warehouse" accent="emerald" summary="The 'Multi-Tenancy Prime Directive,' explained for buyers. Why FastYoke isolates customer data at the OS layer instead of trusting WHERE tenant_id = ?. The architectural commitment under every other security claim."}
  ::cta-button{to="/guides/why-per-tenant-database-files" variant="secondary" size="sm"}
  Read the guide →
  ::
  ::
  ::feature-card{title="FSM as the spine of operations software" icon="project-tracker" accent="violet" summary="Why every serious operations app eventually grows a state machine — and what changes when you start with one instead of bolting it on later. Transitions are checked, guards are first-class, the audit log writes itself."}
  ::cta-button{to="/guides/fsm-as-the-spine" variant="secondary" size="sm"}
  Read the guide →
  ::
  ::
  ::feature-card{title="More architecture pieces coming" summary="The append-only event log explained. Forms vs entities vs jobs. The Marketplace as a contract. Each walks through one load-bearing primitive at a time."}
  ::cta-button{to="/security" variant="secondary" size="sm"}
  See /security
  ::
  ::
::
::

::marketing-section{band="white" maxWidth="4xl"}
#default
<div id="guide-suggestions"></div>

## Suggest a guide

The guides we write are driven by the decision shapes
that mattered most to actual customers. If you're
evaluating FastYoke for a specific situation — a vertical
we haven't covered, a graduation story from a tool we
haven't named, an architectural question we haven't
unpacked, a framework you wish you had before you
committed — **tell us.**

We treat guide requests as a lead signal, not as
something we'll get to eventually. Prospects who want
guidance on *their exact situation* are the prospects we
want to talk to.

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request a guide via early access →
::
::
