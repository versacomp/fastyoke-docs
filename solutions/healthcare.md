---
title: Healthcare on FastYoke — patient flow, triage, EHR/ADT, HIPAA
summary: Patient throughput boards, urgent-care triage, an HL7v2 EHR/ADT connector, on-VM biometrics, and a BAA-backed HIPAA posture. Built for clinics and hospital units that want operational software without sending PHI to a third-party SaaS.
order: 1
---

::hero{eyebrow="Healthcare" title="Operational software for clinics and hospital units" tagline="Patient flow boards, urgent-care triage, an HL7v2 EHR/ADT connector, on-VM biometrics, and a BAA-backed HIPAA posture — all on a tenant database you own."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Who this is for

Clinical operations directors, charge nurses, urgent-care
administrators, and the IT teams supporting them. You have
an EHR for the chart of record; what you don't have is good
software for the **operational layer around the chart** —
which bed a patient is waiting on, who's transporting them,
whether EVS has turned the room, who's on the discharge
worklist this hour. That layer currently lives in a
whiteboard, a shared spreadsheet, a Teams thread, or all
three at once.

## What FastYoke is — and isn't — for healthcare

**FastYoke is operational scaffolding around the chart.**
The Patient Flow Yoke ships four FSM boards out of the box
running on a per-tenant SQLite database with an append-only
event log. The EHR/ADT connector ingests HL7v2 admit /
transfer / discharge messages so the boards stay current.

**FastYoke is not a certified EHR.** It doesn't bill,
doesn't write to the chart, and doesn't claim CEHRT or
Meaningful Use status. The connector is read-only by
construction. If you need a chart of record, FastYoke runs
*around* yours, not in place of it.

::

::marketing-section{band="gray" maxWidth="6xl"}

#eyebrow
What ships today

#title
1. What's currently shipped

#lede
Six surfaces ship now and pair cleanly: four operational FSM boards, one connector to your interface engine, plus the security posture that makes regulated workloads survivable.

#default
<div id="shipped"></div>

::feature-grid{columns="3"}
  ::feature-card{title="Patient Flow Yoke" icon="crm" accent="rose" summary="Four FSM boards: bed placement, patient transport, discharge planning, and bed turnover (EVS). Spine of every throughput conversation."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="Urgent Care Triage" icon="forms" accent="rose" summary="Visit FSM + a 3-rail checkout flow. Run a same-day clinic without sending patient identity to a third-party SaaS."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="EHR / ADT Connector" icon="logistics" accent="rose" summary="HL7v2 admit / transfer / discharge over HTTPS keeps the patient-flow boards live without writing to the chart. Read-only, append-only, PHI-encrypted."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="On-VM biometrics" icon="warehouse" accent="emerald" summary="1:N face recognition runs inside your tenant — no SaaS face-API, no images leaving the box. Useful for staff sign-in and patient match-back."}
  ::cta-button{to="/docs/marketplace" variant="secondary" size="sm"}
  Learn more
  ::
  ::
  ::feature-card{title="HIPAA add-on + BAA" icon="accounting" accent="emerald" summary="Per-tenant AES-256-GCM field-level encryption for tagged PHI/SPI fields. BAA executed on Enterprise / ISV tier as part of HIPAA add-on enablement."}
  ::cta-button{to="/security" variant="secondary" size="sm"}
  Security posture
  ::
  ::
  ::feature-card{title="Region pinning" icon="warehouse" accent="emerald" summary="Pin your tenant to a specific Fly.io region or region group for data-residency obligations (HIPAA-eligible US-only configs available)."}
  ::cta-button{to="/security" variant="secondary" size="sm"}
  Security posture
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

The shipping apps handle the canonical operational layer.
Real hospitals always have **one more thing** that needs to
fit on top. Most extensions don't require an engineer:

- **Custom intake forms (in 30 minutes).** Build a public
  pre-registration form in the Form Designer with
  conditional routing, file upload (insurance card
  scans), and a PDF copy on submit. Wire it to the
  Patient Flow board's intake transition.
- **A new state on an existing board (in an hour).**
  Add a "PendingLabResults" state between Occupied and
  PendingDischarge. The FSM Designer adds the state,
  the guard, and the audit row; no code changes
  needed.
- **A custom dashboard for the charge nurse (in
  an afternoon).** The marketplace ships generic
  list / detail / dashboard blocks. Drop in counts of
  "Patients > 4 hours in Pending state," group by
  unit, expose as a tile on the operator landing.
- **A scheduled escalation when a stage stalls (in two
  hours).** A self-loop transition with a JSONLogic
  guard fires every hour against PendingDischarge
  encounters that have been in state for more than 24
  hours, appending an audit row and notifying case
  management. No webhook, no third-party scheduler.

The low-code work is bounded by the schema. When you need
something the schema can't express, you're in
customization territory — covered next.

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Customize deeply

#title
3. How to make major customizations

#default
<div id="customization"></div>

When the low-code surface isn't enough, two paths:

### In-house engineering

FastYoke ships a **typed SDK** (`@fastyoke/sdk-next`,
`@fastyoke/sdk-vue`, plus a Rust crate for the kernel).
Your engineering team can:

- Build a custom React or Vue admin app against the
  platform API.
- Author custom marketplace apps (Cargo workspace
  crates) for vertical-specific FSM shapes.
- Embed FastYoke in a larger product via the **ejection
  clause** — take your compiled rules and per-tenant
  databases with you whenever you want. No lock-in is
  contractual *and* technical.

For organizations with the engineering bench to absorb
operational software in-house, this is the cheapest
long-term shape. The training [syllabus](/docs/training/syllabus)
is the canonical onboarding curriculum.

### FastYoke Partner engagement

If you don't want to own the build, two partner shapes:

- **[Strategic Partner](/partners/inetko)** — iNetko
  delivers implementation, sales engineering, frontline
  support, and ongoing advisory. Per-tenant consent +
  audit-trail enforcement make their access auditable
  by you, in your tenant.
- **[Channel Partner](/partners)** — for healthcare
  organizations with a specific vertical preference,
  see the partner directory.

Most early healthcare cohorts engage iNetko for the first
30-60 days of implementation. The Strategic Partner
Program's per-tenant consent infrastructure is
specifically designed for regulated workloads — revocation
stops access on the next request, no exception.

::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Honest tradeoffs

#title
What FastYoke isn't for healthcare

#default

- **Not a certified EHR.** You still need one. FastYoke
  runs alongside it.
- **No clinical decision support.** Boards display state;
  they don't recommend care.
- **HIPAA-by-default, not by accident.** The HIPAA add-on
  must be enabled and the BAA executed; PHI tagging is
  the tenant's responsibility. Defaults are *opt-in
  opaque-by-default*, not silently compliant.
- **The ADT connector is read-only.** Charges, orders,
  and chart edits all stay in the EHR. By design — but if
  you want a system that writes to the chart, FastYoke is
  the wrong shape.

If any of those is a deal-breaker, you need a different
class of product, not a different vendor.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

## Next step

FastYoke's general release is scheduled for later this
year. Healthcare workloads are evaluated cohort-by-cohort
— request early access below and we'll set up a 20-minute
scoping call with someone who's wired healthcare tenants
before.

::cta-button{to="/getting-started" variant="primary" size="lg"}
Request early access
::

::cta-button{to="/security" variant="secondary" size="lg"}
Read the security posture
::

For procurement or compliance review packets, email
[security@fastyoke.io](mailto:security@fastyoke.io) and
reference *healthcare* in the subject line.

::
