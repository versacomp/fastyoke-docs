---
title: Professional services on FastYoke — projects, tasks, client work, billing
summary: Operations software for consulting practices, agencies, and project-shaped service businesses. Projects, tasks, time-and-materials billing, and a real audit trail underneath.
order: 5
---

::hero{eyebrow="Professional services" title="Operations software for consulting, agencies, and project shops" tagline="Projects, tasks, client work, time-and-materials billing — and the audit trail your clients ask for when a project review happens. Built for service firms that want operational rigour without enterprise PSA complexity."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Who this is for

Consulting practices, marketing agencies, design studios,
engineering services firms, accounting practices, and any
project-shaped service business with 5-100 staff. Your
current stack is some combination of a project tool
(Asana / Linear / Jira / Trello), a CRM (HubSpot or
nothing), a billing tool (FreshBooks / QuickBooks
Online), and a shared drive. Each tool is fine; the
seams between them are where the work gets lost.

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
What ships today

#title
1. What's currently shipped

#lede
The professional-services stack on FastYoke pairs marketplace apps that already share a primary key: client / project / task / invoice live in the same database, the same audit log, the same RBAC. The seams that lose work in a multi-tool stack don't exist here.

#default
<div id="shipped"></div>

::feature-grid{columns="3"}
  ::feature-card{title="CRM Suite" icon="crm" accent="cyan" summary="The full sales conversion chain — Lead → Opportunity → Quote → Order → Customer. Quotes carry line items that flow into orders; orders activate the customer as a billable client. No re-keying."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Project Tracker" icon="project-tracker" accent="violet" summary="Projects and tasks on a simple board with FSM-driven stage gates. Move a project through New → Active → On Hold → Complete with guards and audit. Tasks move from to-do to done."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Yoke Ledger + GL Pack" icon="accounting" accent="emerald" summary="AR / AP, time-and-materials billing, project-level P&L, AR aging. Time entries on tasks post to AR invoices on the configured cadence. Month-end close runs against the same ledger."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Forms — free" icon="forms" accent="rose" summary="Public RFP-request forms, contact forms, project intake forms. Cloudflare Turnstile + PDF copy on submit. Free forever — the inbound funnel isn't a paid add-on."}
  ::cta-button{to="/free-forms" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Notes & Attachments" icon="accounting" accent="amber" summary="Every entity carries a notes + attachments rail in the inspector. Meeting notes on a customer, scope docs on an opportunity, deliverables on a project — all in one auditable timeline."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Per-tenant RBAC" icon="warehouse" accent="emerald" summary="72-permission catalog with system roles. Admins, project managers, billable team members, and external collaborators each see a different surface. Role changes are audited."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
::
::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Extend without code

#title
2. How to extend with low-code

#default
<div id="low-code"></div>

Every professional-services firm has the same shape with
a different surface — the utilization report your CFO
needs, the project-profitability dashboard your partners
care about, the retainer renewal cadence your client
success team runs. Most don't require an engineer:

- **A utilization dashboard (an afternoon).** Sum time
  entries per team member per week against their target
  billable hours. Drop in a dashboard block; expose as
  a tile on the admin console. The metric refreshes
  against the live ledger.
- **A project-profitability view (an afternoon).** Per
  project, sum invoiced revenue against logged time
  cost. The Yoke Ledger's per-project GL posting makes
  this a single query. Expose it on the Project Tracker
  detail page.
- **Retainer auto-renewals (an hour).** A scheduled
  scheduler-side job re-creates the monthly retainer
  invoice on the contract anniversary. The Yoke
  Ledger's automated-posting rule framework handles
  the line-item logic.
- **A scope-change approval workflow (an hour).** Add
  a `PendingClientApproval` state between a draft
  scope change and an active scope change. The FSM
  guard refuses transitions without a signed
  attachment. Audit trail captures the approval.
- **Expense submission with attachment (an
  afternoon).** Build a public form for team members
  to submit expenses with receipt photos. Submissions
  land in an entity tied to the project; approval flow
  is a simple FSM. Reimbursements post to AP via the
  Yoke Ledger.

When the low-code surface isn't enough — typically when
you need a custom client portal or a specialized
time-tracking device integration — you're in
customization territory.

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Customize deeply

#title
3. How to make major customizations

#default
<div id="customization"></div>

### In-house engineering

Professional services firms often have engineering bench
(especially agencies and consultancies with a technical
practice). The typed SDK
(`@fastyoke/sdk-next`, `@fastyoke/sdk-vue`, plus a Rust
crate for the kernel) enables:

- **Custom client portals.** Most agencies build one —
  a branded surface where clients see their project
  status, deliverables, and invoices. The SDK exposes
  the read APIs; you ship a Next.js or Vue app on top
  with your branding.
- **Direct integrations with non-marketplace tools.**
  Your specific time-tracking app (Harvest, Toggl,
  Everhour), your project management overlay (if you
  keep one), your CRM if you don't migrate off HubSpot.
- **Custom marketplace apps (Cargo workspace crates)**
  for vertical-specific shapes — a creative-services
  resource scheduling app, a technical-services
  retainer management app.
- The **ejection clause** — take your compiled rules
  and per-tenant databases with you whenever you want.
  Useful if your firm is acquired and the acquirer
  has their own platform.

### FastYoke Partner engagement

For firms that don't want to build:

- **[Strategic Partner — iNetko](/partners/inetko)** —
  handles implementation, custom client-portal builds
  if you want one, and ongoing advisory. Per-tenant
  consent + audit-trail enforcement make their access
  auditable by you, in your tenant.
- **Channel Partners** specific to professional
  services aren't named yet. If your firm operates in
  a regulated vertical (accounting, legal, healthcare-
  adjacent consulting), the
  [/channel-partners](/channel-partners) program is
  the right intake.

Most professional-services firms with engineering bench
build the client portal themselves and engage iNetko for
the operator-side implementation. Firms without
engineering bench engage iNetko end-to-end.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
What FastYoke isn't for professional services

#default

- **Not a time-tracking app.** Time entries land in
  FastYoke (manual entry, or via an integration), but
  the timer / idle-detection / desktop-tracker shape
  isn't shipped. If your billing model depends on
  Harvest- or Toggl-style passive tracking, you'll
  integrate one of those tools.
- **Not a resource-scheduling tool.** Project Tracker
  models task lifecycle, not Gantt-style resource
  allocation. If you live in Microsoft Project or
  Smartsheet's Gantt views, you'll find that surface
  missing.
- **No native expense-receipt OCR.** Receipt attachments
  land on entities, but extracting line items from a
  photo requires the WASM scripting tier and a
  third-party OCR service.
- **No native PSA shape.** FastYoke composes a PSA from
  CRM + Project Tracker + Yoke Ledger; if you want a
  purpose-built PSA (Mavenlink, Kantata, BigTime), the
  vertical SaaS will fit your shape more directly out
  of the box.

If any of those is the deal-breaker — *stay on the
purpose-built PSA*. The vertical SaaS exists because the
vertical has specific shape. FastYoke is the right
substrate when you want unified ops + CRM + ledger and
the flexibility to extend each surface independently.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

## Next step

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request early access — professional services
::

::cta-button{to="/partners/inetko" variant="secondary" size="lg"}
Talk to iNetko
::

For implementation help, see the
[Strategic Partner](/partners/inetko) page. For RFP-style
inbound from your clients, the [free Forms](/free-forms)
on Solo tier handle the intake — no credit card.

::
