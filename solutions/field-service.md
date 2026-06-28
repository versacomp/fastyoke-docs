---
title: Field service & HVAC on FastYoke — dispatch, on-site, invoice
summary: Field-service operations for contractors running real crews — HVAC, electrical, plumbing, and multi-trade shops. Dispatch boards, on-site state tracking, invoice-on-completion, and the audit trail when a customer disputes a charge.
order: 2
---

::hero{eyebrow="Field service / HVAC" title="Operational software for field-service contractors" tagline="Dispatch boards, on-site state tracking, invoicing the moment the work is done, and an audit trail every operator can read. Single-tenant-multi-facility or one-tenant-per-location — both shapes ship."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Who this is for

HVAC contractors, electricians, plumbers, locksmiths,
appliance-repair operators, and multi-trade shops with
5-50 field technicians across one or more locations. You
have a dispatcher, an office team, and a customer base
that mixes residential maintenance contracts, commercial
service calls, and the occasional install. Your current
stack is some combination of ServiceTitan, Housecall Pro,
a homegrown spreadsheet, a separate accounting package,
and a lot of Slack messages.

This page is for the moment you're evaluating a
replacement for that stack or looking for the back-of-
house operations layer underneath it.

::

::marketing-section{band="gray" maxWidth="6xl"}

#eyebrow
What ships today

#title
1. What's currently shipped

#lede
The field-service stack on FastYoke is a combination of marketplace apps plus the free Logistics core. Six apps cover intake-to-invoice end-to-end.

#default
<div id="shipped"></div>

::feature-grid{columns="3"}
  ::feature-card{title="Field Service Yoke" icon="field-service" accent="sky" summary="The flagship board for this vertical. Every job moves New → Dispatched → On-site → Invoiced. Dispatcher sees the queue; tech sees their day on their phone."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="CRM Suite" icon="crm" accent="cyan" summary="Customers, equipment-on-site, contract status, service history. Linked to the job entities so a dispatcher opening a job sees the full equipment history without a context switch."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Yoke Ledger + GL Pack" icon="accounting" accent="emerald" summary="AR / AP, invoicing, payment capture, GL posting. When a job moves to Invoiced, the invoice posts to the ledger automatically. Month-end close runs against the same ledger."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Inventory" icon="inventory" accent="amber" summary="Truck stock, parts on hand, reorder points. When a tech uses a part on a job, the inventory deducts. The GL Pack posts COGS on issue if you flip the toggle."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Logistics core (free)" icon="logistics" accent="cyan" summary="Facilities, bins, zones, movements, immutable event log. Free with every tenant — the shared substrate every field-service multi-location operation builds on."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Forms — free" icon="forms" accent="rose" summary="Public maintenance-request forms, online quote requests, contact forms. Cloudflare Turnstile + PDF copy on submit. Free forever; you don't pay extra for intake."}
  ::cta-button{to="/free-forms" variant="secondary" size="sm"}
  Learn more
  ::
  ::
::

::callout{type="info" title="Multi-location shape"}
Running across multiple locations is the question that
throws most field-service software. FastYoke handles it
three ways: **single tenant with multiple facilities**
(most common), **one tenant per location** (separately
managed franchises), or **hub-and-spoke** (rare; mostly
overkill). The [Multi-location HVAC guide](/guides/multi-location-hvac-on-fastyoke)
walks through the call.
::
::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Extend without code

#title
2. How to extend with low-code

#default
<div id="low-code"></div>

Field-service operations are full of small per-shop
quirks — the pricing book, the truck-stock policy, the
maintenance-contract billing cadence. Most don't require
an engineer:

- **Pricing-book authoring in the schema (one
  afternoon).** Use the entity-schema layer to model
  your specific line-item catalog with optional
  add-ons, regional pricing, and maintenance-contract
  discounts. The catalog flows into the Field Service
  Yoke's line-item picker and the Yoke Ledger's
  invoicing.
- **A custom approval step for jobs over a threshold
  (one hour).** Add an `AwaitingApproval` state between
  `On-site` and `Invoiced` for jobs over a dollar
  threshold. The FSM Designer adds the guard
  (`{">": [{"var": "total"}, 1000]}`). No code change.
- **Maintenance-contract auto-scheduling (an
  afternoon).** Use the FSM scheduler to spawn next-
  visit jobs on a contract anniversary. The audit log
  proves the maintenance was scheduled when contracted.
- **Equipment-warranty lookups (one day).** The WASM
  scripting tier (TypeScript on QuickJS in wasmtime)
  lets you call out to the manufacturer's warranty API
  on a job-creation transition. Returns get stored on
  the equipment entity, surfaced in the tech's mobile
  view.

The low-code surface is bounded by what the schema can
express. When the customization is broader than
schema-shape, you're in customization territory — covered
next.

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Customize deeply

#title
3. How to make major customizations

#default
<div id="customization"></div>

### In-house engineering

FastYoke ships a **typed SDK** (`@fastyoke/sdk-next`,
`@fastyoke/sdk-vue`, plus a Rust crate for the kernel).
For field-service contractors with engineering bench
(usually rare in this vertical), the SDK enables:

- Custom tech-facing mobile experiences (the platform
  ships a responsive web UI; a native React Native app
  is a project, not a config).
- Direct integrations with non-marketplace vendors
  (parts distributors, equipment-warranty APIs,
  specific accounting packages beyond Yoke Ledger).
- The **ejection clause** — take your compiled rules
  and per-tenant databases with you whenever you want.
  Useful if your operation grows into a private-equity
  rollup with its own platform.

### FastYoke Partner engagement

For most field-service operations, the right shape is a
Strategic Partner engagement:

- **[iNetko](/partners/inetko)** — our first named
  Strategic Partner — handles schema authoring, FSM
  design for your specific service-call flow,
  integration wiring (payment processor, parts-
  distributor import, equipment-warranty API), and the
  first 60-90 days of operator onboarding. One iNetko
  SE for the first three weeks is enough for most
  single-location pilots.
- See the [Multi-location HVAC guide](/guides/multi-location-hvac-on-fastyoke)
  for the realistic pilot path.

Most field-service prospects engage iNetko before they
engage their own engineering team. The math usually wins
on the partner side — iNetko has done this enough times to
make scoping mistakes that you'd otherwise make yourself.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
What FastYoke isn't for field service

#default

- **No built-in pricing-book module.** Field Service
  Yoke ships a generic line-item flow. If you're used
  to ServiceTitan's built-in pricing-book authoring
  UI, you'll author one in the entity schema layer
  instead. Real but unfamiliar.
- **No native dispatch optimization.** You see the
  queue and you assign manually (or via a simple
  rules-based workflow). Native vehicle-routing
  optimization isn't yet generalized from the Bus
  Routing / Convention Rideshare apps to field service.
- **No native mobile app for techs (yet).** Techs use
  the web console on their phone. It's responsive and
  it works, but it's not a native iOS / Android app.
  Native field-tech mobile is on the roadmap.

If those tradeoffs are deal-breakers — *stay on
ServiceTitan*. The vertical SaaS has those features
because the vertical SaaS is built only for HVAC. FastYoke
gives you the operational substrate, the workflow engine,
and the multi-app integration story for the cases where
the vertical SaaS is overkill or doesn't fit your
specific shape.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

::callout{type="info" title="Run smarter with Yoke"}
**[Yoke](/yoke)** reads your dispatch history and surfaces **briefs** — workflow improvements you approve before anything changes. Weekly digests, No-Show ML, and ServiceTitan / Jobber backfill for cold start. See the [AI hub](/ai) for Yoker RAG and builder tools.
::

::callout{type="info"}
**Deploying to mobile devices?** [FastYoke Substrate](/substrate) ships
native Android and iOS apps to your field fleet without App Store review
— OTA-updatable, MDM-compatible, and available at the Team+ tier.
Early access Q1 2027.
::

## Next step

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request early access
::

::cta-button{to="/partners/inetko" variant="secondary" size="lg"}
Talk to iNetko
::

Already in retail / POS / food service paired with field
service? See [Pay n Go Systems](/partners/pay-n-go-systems)
— our first named Channel Partner — for the integrated
storefront-plus-field-tech shape.

::
