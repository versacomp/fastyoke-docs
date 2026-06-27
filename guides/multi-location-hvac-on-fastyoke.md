---
title: Multi-location HVAC on FastYoke — FastYoke
summary: Field-service operations for an HVAC contractor running across multiple locations. Dispatch, on-site state, invoicing, and the marketplace apps that wire it together.
order: 9
---

::hero{eyebrow="Vertical playbook" title="Multi-location HVAC on FastYoke" tagline="What the day-to-day stack looks like for an HVAC contractor running real field operations — dispatch boards, on-site state tracking, invoicing the moment the work is done, and the audit trail when a customer disputes a charge."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Who this is for

You run an HVAC contracting business with 5–50 field
technicians across one or more locations. You have a
dispatcher, an office team, and a customer base that's a
mix of residential maintenance contracts, commercial
service calls, and the occasional install. Your current
stack is some combination of ServiceTitan, Housecall Pro,
a homegrown spreadsheet, a separate accounting package,
and a lot of Slack messages.

This guide is for the moment you're either evaluating a
replacement for that stack or looking for the back-of-
house operations layer underneath it.

## The shape of the work

HVAC operations have a fairly stable rhythm:

- **Intake.** A customer calls or submits a form.
- **Triage.** The dispatcher decides which tech, which
  truck, which day.
- **Dispatch.** The tech gets a job on their phone with
  the customer's address, the equipment history, and the
  parts list.
- **On-site.** Diagnose, quote, get approval, do the
  work.
- **Invoice.** Capture payment on-site or email the
  customer an invoice they can pay.
- **Followup.** Schedule the next maintenance visit.
  Close the loop on the work order.

Every step has state. Every state change matters. Most
"why didn't we get paid?" / "why did the tech show up at
the wrong address?" / "why is this maintenance contract
30 days overdue?" fail-modes trace back to a state
transition that didn't happen or got mis-recorded.

FastYoke models that rhythm directly.

## The apps you install

The HVAC stack on FastYoke is a combination of marketplace
apps plus the free Logistics core:

### Field Service Yoke

The flagship for this vertical. Schedule jobs, dispatch
your crew, track each job through **New → Dispatched →
On-site → Invoiced**. The dispatcher sees the queue; the
tech sees their day on their phone. Every transition
fires guards (was the tech qualified for this work? did
the customer approve the estimate?) and writes an
append-only audit row.

### CRM Suite

Customers, equipment-on-site, contract status, service
history. Linked to the job entities so a dispatcher
opening a job sees the full equipment history without a
context switch.

### Yoke Ledger + GL Pack

AR / AP, invoicing, payment capture, GL posting. When a
job moves to **Invoiced**, the invoice posts to the
ledger automatically. When the customer pays (in-app or
via the payment connector), the payment posts to AR.
Month-end close runs against the same ledger.

### Inventory

Truck stock, parts on hand, reorder points. When a tech
uses a part on a job, the inventory deducts; when a
truck restocks, inventory increments. The GL Pack posts
COGS on issue if you flip the toggle.

### Forms — free

Public maintenance-request forms, contact forms, online
quote requests. Cloudflare Turnstile + PDF copy on
submit. Free forever; you don't pay extra for the intake
funnel.

## Multi-location specifics

Running across multiple physical locations is the
question that throws most field-service software. FastYoke
handles it three ways depending on shape:

- **Single tenant, multiple "facilities."** Each location
  is a `Facility` entity in the free Logistics core. Techs
  are scoped by facility. Dispatching is per-facility but
  the customer database is shared.
- **One tenant per location.** Each location runs as its
  own FastYoke tenant. Useful if locations are
  separately-managed franchises with their own books.
  The fleet-of-files architecture means each location's
  data lives in its own database file.
- **Hub-and-spoke.** Headquarters runs a master tenant;
  each location is a sub-tenant under it. Channel Partner
  shape, technically possible, usually overkill for
  HVAC.

For most multi-location HVAC operators, **single tenant
with multiple facilities** is the right shape. The audit
trail spans the whole operation, the customer DB is
shared (you don't lose history when a customer moves to a
job-site at a different location), and the dispatcher's
view can be scoped per-facility.

## What an honest deployment looks like

The realistic path for an operator evaluating FastYoke
for HVAC:

1. **Pilot one location.** Install Field Service +
   CRM + Yoke Ledger. Migrate one location's customer
   database and run the office for 30 days alongside
   your current stack. Both systems get the data.
2. **Bring in a Strategic Partner.** [iNetko](/partners/inetko)
   handles the schema authoring, the FSM design for
   your specific service-call flow, and the integration
   wiring (payment processor, the parts-distributor
   import, the equipment-warranty API). One iNetko SE
   for the first three weeks is enough for most
   single-location pilots.
3. **Expand to all locations.** Once one location runs
   green for a month, the playbook repeats per
   location. You don't roll out everything at once;
   you roll out the locations in priority order.
4. **Retire the old stack.** When all locations are on
   FastYoke and the office team trusts the new system
   more than the old one, the old stack goes. Typically
   60–90 days from pilot start.

## Honest tradeoffs

FastYoke is a workflow platform, not a vertical SaaS
purpose-built for HVAC:

- **No built-in pricing-book module.** Field Service
  Yoke ships a generic line-item flow that handles
  most pricing-book cases via the entity-schema layer,
  but if you're used to ServiceTitan's built-in pricing-
  book authoring you'll need to either author one in
  the schema or live with a slightly heavier
  workflow.
- **No native dispatch optimization.** You see the queue
  and you assign manually (or via a simple rules-based
  workflow). Native vehicle-routing optimization isn't in
  scope for v1. (We have it for the Bus Routing and
  Convention Rideshare apps; it's not yet generalized
  to field service.)
- **No mobile app for techs (yet).** Techs use the web
  console on their phone. It works, it's responsive, but
  it's not a native iOS / Android app. Native
  field-tech mobile is on the roadmap.

If those tradeoffs are deal-breakers — *stay on
ServiceTitan*. The vertical SaaS has those features
because the vertical SaaS is built only for HVAC. FastYoke
gives you the operational substrate, the workflow engine,
and the multi-app integration story for the cases where
the vertical SaaS is overkill or doesn't fit your
specific shape.

## How to start

1. Request [early access](/getting-started) and mention
   HVAC in the form. We'll route you to iNetko for the
   scoping conversation.
2. Read the [Channel Partner](/partners/pay-n-go-systems)
   page if your operation also sells equipment (POS
   side-by-side with field service is a common shape).
3. Compare against the [Field Service](/docs/marketplace)
   app's capability list to make sure the v1 feature
   surface covers your bottom-three workflows.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

For a 20-minute scoping conversation with a Strategic
Partner SE who's onboarded HVAC operators before, request
[early access](/getting-started) and reference HVAC in
the form.

::
