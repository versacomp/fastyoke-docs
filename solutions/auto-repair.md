---
title: Auto & repair on FastYoke — intake to handoff on one board
summary: Operations software for auto-tech shops, body shops, motorcycle and small-engine repair, and equipment-service operators. Intake to handoff on one board with parts, labor, and invoicing wired in.
order: 6
---

::hero{eyebrow="Auto & repair" title="Operations software for repair shops" tagline="Every job tracked from intake through handoff on a single board, with parts inventory, labor tracking, and invoicing wired in. Built for repair shops that want one source of truth instead of three half-integrated tools."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Who this is for

Independent auto-tech shops, body shops, motorcycle and
small-engine repair, RV and equipment service, marine
shops, and any repair operation running 2-30 technicians.
Your current stack is some combination of Mitchell 1 /
ALLDATA for repair info, an invoicing tool, a parts-
distributor portal, and a paper work order on the
technician's bench. You want one board that holds the
work from drop-off to pickup with the audit trail of who
did what and when.

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
What ships today

#title
1. What's currently shipped

#lede
The auto-repair stack on FastYoke adapts the Field Service Yoke for shop-floor workflows. Customer + vehicle in CRM, parts in Inventory, labor in the workflow, invoicing on customer approval — all on the same per-tenant database with one audit trail.

#default
<div id="shipped"></div>

::feature-grid{columns="3"}
  ::feature-card{title="Field Service Yoke (shop-adapted)" icon="field-service" accent="sky" summary="Repair workflow: intake → diagnose → estimate → approval → repair → QC → handoff. Service writer sees the bay queue; tech sees their bay assignment. Every transition is audited."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="CRM Suite (customer + vehicle)" icon="crm" accent="cyan" summary="Customers, vehicles (or equipment) with VIN / serial / make / model, service history per vehicle. The service writer opening a job sees every past visit, every part, every issue — in one place."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Inventory" icon="inventory" accent="amber" summary="Parts on hand per shop bin, reorder points, parts consumption per job, COGS posting if you flip the toggle. Stockouts surface on the job-create transition so the tech doesn't start work without parts."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Yoke Ledger + GL Pack" icon="accounting" accent="emerald" summary="Estimate becomes invoice on customer approval. Payment capture posts to AR. Month-end close runs against the same ledger your accountant reads. Sales tax computed per jurisdiction."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Forms — free" icon="forms" accent="rose" summary="Public appointment-request forms, online estimate-request forms, contact forms. Cloudflare Turnstile + PDF copy on submit. Free forever — your intake funnel isn't a paid add-on."}
  ::cta-button{to="/free-forms" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Customer status web links" icon="forms" accent="rose" summary="A customer-facing link per job shows live status (in diagnosis / awaiting approval / in repair / ready). No customer portal to manage — the link is the portal. Status updates in real time."}
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

Every shop has its specific workflow wrinkles — the
multi-bay scheduling policy, the warranty-claim
handoff, the safety-inspection holdover for state
compliance. Most don't require an engineer:

- **Multi-bay scheduling (an afternoon).** Use the
  entity schema layer to model bays as facilities and
  tech assignments as guards on the dispatch
  transition. Service writer drags jobs to bays; the
  guard enforces tech qualifications.
- **A safety-inspection holdover state (an hour).**
  Add a `PendingInspection` state between Diagnose and
  Estimate for vehicles in jurisdictions requiring a
  state safety inspection. The FSM Designer adds the
  state and the guard; no code change.
- **Equipment-warranty lookups (one day).** The WASM
  scripting tier (TypeScript on QuickJS in wasmtime)
  lets you call out to the manufacturer's warranty
  API on the diagnose transition. Returns get stored
  on the vehicle entity; warranty-eligible labor and
  parts surface automatically.
- **Parts-distributor catalog sync (one day).** The
  scripting tier can pull SKU + price + availability
  from your distributor's API on a schedule. The
  Inventory app's catalog stays current without
  manual entry.
- **Maintenance-reminder cadence (an hour).** A
  scheduled job runs monthly, computes vehicles due
  for routine service based on last-visit date plus
  mileage estimate, emails the customer a
  pre-populated appointment-request link. The Yoke
  Ledger's reporting framework formats the email.

When the low-code surface isn't enough — typically a
custom shop-floor device integration or a deep DMS
(Dealer Management System) integration — you're in
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

Independent repair shops rarely have engineering bench;
this section is for the multi-location chains and
franchise operations that do. The typed SDK
(`@fastyoke/sdk-next`, `@fastyoke/sdk-vue`, plus a Rust
crate for the kernel) enables:

- Custom tech-facing tablet experiences for the bay
  floor (the platform ships a responsive web UI; a
  native iPad app is a project, not a config).
- Direct integrations with non-marketplace vendors —
  Mitchell 1 / ALLDATA for repair info lookups, DMS
  systems if you're attached to a dealership group,
  state DMV / inspection APIs for compliance.
- Custom marketplace apps (Cargo workspace crates) for
  vertical-specific shapes — body-shop estimating
  with paint-time calculators, motorcycle-shop
  seasonal-storage workflows, RV-shop multi-system
  repair orders.
- The **ejection clause** — take your compiled rules
  and per-tenant databases with you whenever you want.
  Useful if your operation is rolled into a
  private-equity-backed group with its own platform.

### FastYoke Partner engagement

For shops that prefer a delivered shape:

- **[Strategic Partner — iNetko](/partners/inetko)** —
  handles schema authoring, FSM design for your
  specific shop-floor flow, integration wiring (parts
  distributor, payment processor, repair-info lookups),
  and the first 60-90 days of operator onboarding.
  One iNetko SE for the first three weeks is enough
  for most single-shop pilots.
- **Channel Partners** specific to auto / repair
  aren't yet named. The
  [/channel-partners](/channel-partners) program
  is the right intake if your shop also sells parts
  or accessories alongside repair services.

Most repair-shop prospects engage iNetko before they
engage their own engineering team. The math usually
wins on the partner side — iNetko has the platform
internals deeper than a first-time engineering team
can ramp on.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
What FastYoke isn't for auto & repair

#default

- **No native VIN decoder.** The CRM stores the VIN
  as a string; decoding it into make / model / year /
  trim requires a third-party VIN-decoder API call
  via the scripting tier.
- **No built-in parts-distributor catalog.** You bring
  one — either via the scripting-tier sync above or
  manual catalog import. The platform doesn't ship a
  preloaded WorldPac / NAPA / OEM catalog.
- **No native DMV / state-inspection integrations.**
  These vary jurisdiction-by-jurisdiction and are SDK-
  built rather than out-of-box.
- **No native paint-time calculator** (body shops).
  Estimating tools like Mitchell or CCC are SDK-
  integrable, not out-of-box marketplace apps.
- **Not a DMS replacement.** If you're attached to a
  dealership group running a full DMS (CDK / Reynolds
  & Reynolds / Dealertrack), FastYoke runs alongside
  it for the service-bay workflow, not in place of it.

If any of those is the deal-breaker — *stay on Mitchell
1 / ALLDATA paired with a DMS*. The vertical SaaS exists
because the vertical has specific shape. FastYoke fits
the cases where the vertical SaaS is overkill or doesn't
fit your specific shop's workflow.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

## Next step

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request early access — auto & repair
::

::cta-button{to="/partners/inetko" variant="secondary" size="lg"}
Talk to iNetko
::

For multi-location or multi-bay operations, see the
[Strategic Partner](/partners/inetko) page. For repair
shops that also sell parts or accessories, the
[Pay n Go Systems](/partners/pay-n-go-systems) Channel
Partner handles the POS side alongside the FastYoke
service-bay workflow.

::
