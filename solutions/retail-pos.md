---
title: Retail, POS & food service on FastYoke — storefront and counter operations
summary: Multi-location retail with unified inventory and customer flows. Point-of-sale and kiosk deployments paired with payment hardware. Quick-serve, casual, and full-service restaurants — order capture, kitchen-display routing, pickup / delivery, and the back-of-house accounting.
order: 3
---

::hero{eyebrow="Retail / POS / food service" title="Storefront and counter operations on FastYoke" tagline="Multi-location retail, point-of-sale, and food-service ops — paired with Pay n Go Systems for the hardware, payment, and rollout motion. Storefront-shaped operators get a complete stack."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Who this is for

Multi-location retail operators (specialty, boutique,
convenience), point-of-sale operators (counter, kiosk,
self-serve), and food-service operators (quick-serve,
casual, full-service) running 1-50 locations. Your
current stack is some combination of Square, Toast,
Clover, Shopify POS, Lightspeed, a separate accounting
package, and a half-dozen spreadsheets. You've outgrown
the all-in-one shape — your business needs more
flexibility than the vertical SaaS gives you, but you
don't want to build everything from scratch.

::

::marketing-section{band="gray" maxWidth="6xl"}

#eyebrow
What ships today

#title
1. What's currently shipped

#lede
Retail and food-service operations span counter, kitchen, back office, and customer. FastYoke pairs marketplace apps with the Pay n Go Channel Partner motion to cover the whole shape.

#default
<div id="shipped"></div>

::feature-grid{columns="3"}
  ::feature-card{title="CRM Suite" icon="crm" accent="cyan" summary="Customers, loyalty profiles, purchase history. Linked to the order and invoice entities so a counter operator sees the customer's history at the moment they tap in."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Inventory" icon="inventory" accent="amber" summary="Per-location stock, reorder points, low-stock alerts. Cross-location transfers via the free Logistics core. The right shape for a small chain that's outgrown spreadsheets."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Yoke Ledger + GL Pack" icon="accounting" accent="emerald" summary="AR, AP, sales tax, GL posting. Daily Z-out from POS posts directly to the ledger; month-end close runs against the same books your accountant reads."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Order workflow" icon="project-tracker" accent="violet" summary="Order capture → preparation → ready → fulfilled. Kitchen-display routing for food service; pickup / delivery state for omni-channel retail. Every transition is audited."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Forms — free" icon="forms" accent="rose" summary="Public catering-inquiry forms, online reservation forms, catalog request forms. Cloudflare Turnstile + PDF copy on submit. Free forever."}
  ::cta-button{to="/free-forms" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Pay n Go partnership" icon="logistics" accent="cyan" summary="Hardware selection, in-store deployment, payment integration, and ongoing support — delivered by our first named Channel Partner. Turnkey for retail / POS / food service."}
  ::cta-button{to="/partners/pay-n-go-systems" variant="secondary" size="sm"}
  Visit the partner page
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

Retail and food-service operations have endless per-shop
quirks — the loyalty program, the discount-stacking
policy, the kitchen-display priority rules. Most don't
require an engineer:

- **Loyalty-tier authoring (an afternoon).** Use the
  entity schema layer to model customer tiers (Bronze /
  Silver / Gold), the per-tier discount and free-item
  policies, and the auto-promotion rules. The catalog
  flows into the order workflow's pricing step.
- **A kitchen-display priority queue (an hour).** Add a
  rule on the order workflow's `Preparation` state that
  prioritizes orders by ETA + customer tier. The FSM
  Designer adds the sort guard; no code change.
- **A daily Z-out automation (an hour).** A scheduled
  job runs at close, sums the day's orders, posts a
  single journal entry to the ledger, and emails the
  manager. The Yoke Ledger's automated-posting rule
  framework handles it.
- **A multi-location stock-transfer workflow (an
  afternoon).** When one location runs low, the
  Inventory app fires a transfer request to the
  nearest sister location with stock. Approval
  workflow + transfer FSM. Driven by the schema.

When the low-code surface isn't enough — typically when
you need a custom kitchen-display device integration or a
specialized payment hardware flow — you're in
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

Retail and food-service operations rarely have in-house
engineering benches; this section is here for
completeness. If you do have engineers:

- The typed SDK (`@fastyoke/sdk-next`, `@fastyoke/sdk-vue`,
  Rust kernel crate) lets you build custom counter,
  kiosk, or kitchen-display experiences against the
  platform API.
- Custom marketplace apps (Cargo workspace crates) for
  niche payment processors or specialized hardware
  protocols beyond what the Channel Partner integrates.
- The **ejection clause** — take your compiled rules
  and per-tenant databases with you. Important for
  multi-brand operators who eventually want to lift
  the platform into their own infrastructure.

### FastYoke Partner engagement

For retail / POS / food service, the canonical shape is
the Channel Partner motion:

- **[Pay n Go Systems](/partners/pay-n-go-systems)** —
  our first named Channel Partner — delivers hardware
  selection (POS terminals, kitchen displays, kiosks),
  in-store deployment, payment integration, and
  ongoing support. They run the rollout; FastYoke runs
  the platform.
- **[Strategic Partner option](/partners/inetko)** —
  for retail operators that want SE-led implementation
  instead of a channel rollout, iNetko delivers the
  schema authoring and platform onboarding. Less
  common in this vertical but available.

Most retail / POS / food-service prospects engage Pay n Go
because the hardware + payment + deployment work is the
80% of the project; the FastYoke platform configuration
is the 20%. Channel Partner shape matches that ratio.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
What FastYoke isn't for retail / POS / food service

#default

- **Not a turnkey POS terminal.** The hardware and the
  payment-processor integration come from Pay n Go (or
  your existing POS vendor if you bring one). FastYoke
  is the platform underneath, not the box on the
  counter.
- **No built-in kitchen-display SDK (yet).** Kitchen-
  display routing works via the order workflow, but
  the device-side integration is a Pay n Go scope
  item, not an out-of-box marketplace app.
- **Not a delivery-platform aggregator.** If you take
  Uber Eats, DoorDash, and GrubHub orders, those
  integrations sit alongside FastYoke (or via Pay n Go),
  not native to the platform.
- **No native multi-currency for international ops
  (yet).** Single-currency tenants for now. International
  multi-currency support is on the roadmap as part of
  the Yoke Ledger v2 cycle.

If any of those is the deal-breaker — *stay on Toast or
Square or whatever vertical SaaS already covers it*. The
vertical SaaS exists because the vertical has specific
shape; FastYoke fits when you want flexibility *plus* a
real back-office and multi-location story underneath.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

## Next step

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request early access
::

::cta-button{to="/partners/pay-n-go-systems" variant="secondary" size="lg"}
Talk to Pay n Go Systems
::

For multi-location retail or food-service operators
evaluating FastYoke, [Pay n Go](/partners/pay-n-go-systems)
is the right first conversation. They scope the hardware
+ deployment, and we scope the platform together.

::
