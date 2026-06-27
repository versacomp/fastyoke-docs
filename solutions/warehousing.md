---
title: Warehousing & 3PL on FastYoke — WMS / TMS / 3PL stack
summary: Multi-client warehouses, four-wall WMS, transportation management, and the free Logistics core every logistics app sits on. Built for warehouses and 3PL operators that want a unified stack across the four walls and the road.
order: 4
---

::hero{eyebrow="Warehousing & 3PL" title="Warehouse, transport, and 3PL operations on FastYoke" tagline="Receive, putaway, pick, pack, ship — plus rating, labels, and tracking — on per-tenant databases and the free Logistics core. Built for warehouses and 3PL operators that want a unified stack across the four walls and the road."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Who this is for

Warehouse operators (B2B distribution, e-commerce
fulfillment, food and beverage), multi-client 3PL
providers, and transportation managers running 1-20
facilities. You've outgrown a spreadsheet but the
enterprise WMS / TMS stack is overbuilt and overpriced
for your shape. Or you're a 3PL standing up a new client
and want a tenant-isolated platform that scales with the
portfolio.

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
What ships today

#title
1. What's currently shipped

#lede
The warehousing stack on FastYoke is a combination of three marketplace apps on top of the free Logistics core. Receive-to-ship, parcel and multi-carrier shipping, and multi-client 3PL billing — all running on a per-tenant database with an append-only event log.

#default
<div id="shipped"></div>

::feature-grid{columns="3"}
  ::feature-card{title="Warehouse Management" icon="warehouse" accent="amber" summary="Four-wall ops: receive, putaway, pick, pack, ship, cycle count. Guided scan console for each step. Soft-reservation availability so two pickers never grab the same unit."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Transportation Management" icon="logistics" accent="cyan" summary="Parcel and multi-carrier shipping with rating, labels, and tracking. Carrier-agnostic via the ShippingProvider trait. EasyPost / Shippo-class integrations plug in cleanly."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="3PL app" icon="warehouse" accent="violet" summary="Multi-client warehouses on one operator tenant. Storage-snapshot + per-movement-handling billing posts directly to a draft AR invoice. Per-client audit trail and reporting."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Logistics core (free)" icon="logistics" accent="cyan" summary="Facilities, bins, zones, movements, immutable event log. Free with every tenant. The shared substrate every logistics app on FastYoke sits on top of."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Yoke Ledger + GL Pack" icon="accounting" accent="emerald" summary="AR / AP invoicing posted directly from movement-based billing runs. Month-end close runs against the same ledger your accountant reads."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="CRM Suite + Forms" icon="crm" accent="rose" summary="Customer / client records linked to warehouse-client entities. Public RFP and intake forms for new client onboarding. Free Forms forever."}
  ::cta-button{to="/free-forms" variant="secondary" size="sm"}
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

Warehouse operations always have one more wrinkle — the
specific receive-tolerance policy, the client-facing
reporting cadence, the SLA-driven exception handler. Most
extensions don't require an engineer:

- **Custom putaway rules (an hour).** Use the FSM
  Designer to add a guard on the putaway transition —
  oversize SKUs go to specific zones, hazmat SKUs go to
  isolated bins, fast-mover SKUs go to forward pick.
  The schema captures the policy; no code change.
- **A client-facing dashboard (an afternoon).** Drop in
  list / detail / dashboard blocks from the marketplace.
  Expose movement counts, in-stock summaries, and
  shipment status for one warehouse-client — the
  warehouse_client_id dimension scopes the view
  automatically.
- **Automated low-stock alerts (an hour).** A scheduled
  job runs nightly, computes available stock per SKU
  per warehouse-client (`qty_on_hand − Σ reserved`),
  emails the client a CSV of items below their
  reorder threshold. Yoke Ledger's reporting framework
  handles the formatting.
- **Carrier-specific rate shopping (one day).** The
  WASM scripting tier (TypeScript on QuickJS in
  wasmtime) lets you call out to a custom rating
  endpoint on the shipment-creation transition. Useful
  if you negotiated a contract carrier outside the
  default EasyPost / Shippo catalog.
- **Per-client billing-cycle automation (one day).**
  The 3PL app's `tpl_billing_runs` table schedules
  per-client billing on a configurable cadence. The
  result posts directly to AR invoices in the Yoke
  Ledger.

When the low-code surface isn't enough — typically when
you need a custom WCS / WES integration or a specialized
carrier protocol — you're in customization territory.

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Customize deeply

#title
3. How to make major customizations

#default
<div id="customization"></div>

### In-house engineering

For warehouses with engineering bench (less common
mid-market, common at the enterprise scale), the
**typed SDK** (`@fastyoke/sdk-next`, `@fastyoke/sdk-vue`,
plus a Rust crate for the kernel) enables:

- Custom RF-gun or scanner-device integrations beyond
  the default web-on-mobile console.
- WCS / WES (Warehouse Control / Execution System)
  hooks for automated material-handling equipment —
  conveyors, sorters, AS/RS.
- Direct integrations with carriers that aren't in the
  default ShippingProvider catalog (private freight
  contracts, specialized parcel carriers, regional
  LTL).
- Custom marketplace apps (Cargo workspace crates) for
  vertical-specific WMS shapes (cold-chain, hazmat,
  high-touch returns).
- The **ejection clause** — take your compiled rules
  and per-tenant databases with you whenever you want.
  Useful if your operation grows into a private-equity
  warehousing rollup with its own platform.

### FastYoke Partner engagement

For warehouses that prefer a delivered-shape engagement:

- **[Strategic Partner](/partners/inetko)** — iNetko
  handles schema authoring, FSM design for your
  specific receive / pick / ship flow, integration
  wiring (carrier accounts, the WMS-to-ERP feed,
  customer EDI), and the first 60-90 days of operator
  onboarding. Per-tenant consent + audit-trail
  enforcement make their access auditable by you, in
  your tenant.
- **Channel Partners** specific to warehousing aren't
  yet named. If your operation includes a strong
  vertical fit (cold-chain, e-commerce-fulfillment-
  focused, hazmat-specialized), the
  [/channel-partners](/channel-partners) program is
  the right intake.

Most warehouses engage iNetko for the first
implementation phase. Once the platform is steady, the
ongoing operator team takes over while iNetko stays on
retainer for the harder integration work.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
What FastYoke isn't for warehousing

#default

- **Not a Yard Management System (YMS).** Yard moves,
  dock-door scheduling, and trailer pools are outside
  the WMS scope today. Most mid-market operations
  handle this in a spreadsheet alongside the WMS;
  enterprise operations should plan for a separate YMS
  alongside FastYoke.
- **No native voice-pick (yet).** Pickers use the
  web-on-mobile guided scan console. Voice-pick
  hardware integrations are SDK-extensible but not
  shipped out of the box.
- **No native robotics integrations.** AGVs, AMRs, and
  goods-to-person systems integrate via the SDK; no
  out-of-box marketplace apps for specific vendors.
- **Single-currency billing for 3PL.** Yoke Ledger
  ships single-currency. International 3PL operations
  with multi-currency client billing should wait for
  the Yoke Ledger v2 cycle or run a separate ledger
  alongside the FastYoke billing pipeline.
- **No native WMS-to-ERP feed.** The integration is
  SDK-built; we don't ship a SAP / Oracle / NetSuite
  marketplace connector. iNetko handles this in
  practice.

If any of those is the deal-breaker — *stay on Manhattan
Associates or Blue Yonder*. The enterprise WMS exists
because the enterprise has those specific shapes.
FastYoke is the right substrate when you want a unified
WMS + TMS + 3PL stack at mid-market complexity without
six-figure annual licensing.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

## Next step

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request early access — warehousing
::

::cta-button{to="/partners/inetko" variant="secondary" size="lg"}
Talk to iNetko
::

For procurement or compliance review packets, email
[security@fastyoke.io](mailto:security@fastyoke.io) and
reference *warehousing* in the subject line.

::
