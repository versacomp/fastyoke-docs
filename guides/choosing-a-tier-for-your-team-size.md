---
title: Choosing a tier for your team size — FastYoke
summary: An opinionated walkthrough of the FastYoke tier ladder. Which tier fits your team today, which one fits in six months, and the upgrade triggers worth watching.
order: 8
---

::hero{eyebrow="Decision framework" title="Choosing a tier for your team size" tagline="The pricing page tells you what each tier costs. This guide tells you which one fits the team you actually have — and which one fits the team you're about to become."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## How to read this

FastYoke ships six commercial tiers: **Solo, Starter,
Team, Pro, Enterprise, Fleet.** The
[pricing page](/pricing) gives you the feature matrix and
the resource caps. This guide gives you the *fit* — which
tier is right for the team you have, and the signals that
say it's time to upgrade.

If you'd rather skip ahead and talk to someone about
your specific situation, request
[early access](/getting-started) and we'll route you to a
Strategic Partner SE.

## The two questions that pick the tier

Almost all of tier selection collapses to two questions:

1. **Who's running the workflow?** One person? A small
   team? A regulated organization? A reseller portfolio?
2. **What does failure look like?** A missed lead? A
   broken handoff? A compliance breach? A multi-tenant
   billing dispute?

The answer to those two questions tells you which tier
fits — usually within one row of the matrix.

## Solo — one person, side projects, hobbyist scale

**Fit:** you're a side-hustler, hobbyist, freelancer, or
just kicking the tires. Forms are free forever, with no
asterisk. You don't need a team, you don't need an audit
trail anyone besides you will read, and you don't need
custom branding.

**Failure mode:** the public form goes down for ten
minutes. You shrug.

**Signals to upgrade:**

- You added a second person to the tenant.
- You started getting submissions you'd be unhappy to
  lose.
- You started invoicing customers based on form
  submissions.

## Starter — the smallest businesses that pay attention to numbers

**Fit:** one-person business, a few hundred submissions a
month, basic recordkeeping, a simple form-to-workflow
flow. You care that the platform doesn't lose your data,
but you don't yet need fine-grained access control.

**Failure mode:** you missed an order because the email
notification didn't fire. You're annoyed; the customer
got a refund.

**Signals to upgrade:**

- You hired someone and now have a second operator.
- You're up against the transaction or storage cap.
- You started integrating with a payment processor or
  accounting system.

## Team — small operations team, real workflows, real consequences

**Fit:** 3–10 operators sharing the work. You have FSM
workflows that matter — jobs that move through states,
forms feeding intake, an audit trail that someone might
actually need to read someday. You want RBAC, you want
real branding, and you want to know who did what.

**Failure mode:** a transition fired twice and a partner
got double-charged. The customer-success person spent
the morning on the phone.

**Signals to upgrade:**

- You added a second department (sales + ops, ops +
  fulfillment).
- You started needing per-user permissions, not just
  per-tenant.
- You need to plug into the marketplace apps that gate
  on Pro+ tier.

## Pro — multiple departments, marketplace apps, real volume

**Fit:** 10–50 operators across departments. You've
installed marketplace apps (CRM Suite, Yoke Ledger, WMS,
Field Service, Patient Flow). You have real customer
data with PII tagging on. You're starting to think about
the audit story for SOC 2 or another framework.

**Failure mode:** a PII column accidentally rendered in
a server log. Your CTO needs to write an apology.

**Signals to upgrade:**

- You signed an enterprise customer that wants a BAA or
  region pinning.
- Your compliance team starts asking about Type II.
- A regulated workload (healthcare, financial services,
  government) entered scope.

## Enterprise — regulated, audited, BAA-eligible

**Fit:** mid-market or enterprise organization. PHI, SPI,
or otherwise-sensitive data in scope. Region pinning,
BAA execution, the PII encryption add-on, optional HIPAA
add-on. Your procurement process is real.

**Failure mode:** a compliance review surfaces a gap in
your data-residency posture. You're back at the
negotiation table with the prospect, and time has slipped.

**Signals to upgrade:**

- You're standing up a portfolio of tenants for a
  customer base (white-label, reseller, multi-brand).
- You need the metered-only commercial shape (zero
  base, all usage).
- You're shipping a vertical SaaS product *on top of*
  FastYoke and your customers are the end-tenants.

## Fleet — portfolio operators, ISVs, channel partners

**Fit:** you run dozens or hundreds of tenants on
FastYoke. You're a Channel Partner, an ISV, or a
multi-brand operator. You need the fleet-of-files
operational story: tenant provisioning automation,
billing rev-share, branded operator surfaces, a
tenant-fleet management console.

**Failure mode:** rolling out a platform change to 200
tenants needs to be safe. You can't manually QA each
one.

**Signals to "upgrade":** this is the top of the ladder.
The signals here are sideways — into Strategic Partner
status, deeper consulting engagement, or a custom
commercial structure beyond the published tiers.

## The two upgrade triggers nobody talks about

Most of the tier decision is driven by the matrix on the
[pricing page](/pricing). Two triggers aren't on that
matrix and bite teams that miss them:

### Trigger 1: your first regulated customer

If a customer signs that needs BAA, SOC 2 evidence, or
region pinning, you've graduated from Team / Pro to
**Enterprise** *before* you've felt the volume reason
for it. Don't try to retrofit. The compliance posture is
either part of the contract from day one or it's a
re-papering exercise later.

### Trigger 2: your second tenant is for a different customer

If you started on Team or Pro and you're now spinning up
a tenant for *someone else's brand*, you've graduated
into **Fleet** semantics. Same architectural primitives,
different commercial shape. Doing it on Pro tier means
you'll be re-platforming the operator surface within a
year.

## Honest tradeoffs

The tier ladder is real. So is the friction of moving
between rungs:

- **Upgrading mid-deal is expensive.** Negotiating a
  Pro→Enterprise jump while the customer's MSA is being
  redlined is the worst time to figure out which tier
  you need. Better to start at the higher rung if the
  customer profile points there.
- **Downgrading is hard.** Resource caps tighten when
  you drop a tier. If you've grown into the higher tier
  and can't fit back into the lower, you'll be
  re-architecting, not just changing a number.
- **The marketplace gating is real.** Some apps (WMS,
  HIPAA add-on, EHR connector) are gated to Pro+ or
  Enterprise. Picking a tier below the gate means you
  can't install the app, full stop.

If you're between two rungs, **err one tier high**
during early access. The pricing
[during pre-GA](/getting-started) is operator-direct, so
the math is friendly. Locking in the right shape now is
worth more than the spread.

## How to start

1. Answer the two questions at the top of this guide.
2. Map the answer to the tier section.
3. If you landed on Enterprise or Fleet, request
   [early access](/getting-started) and reference your
   tier in the form. We'll route to the right Strategic
   Partner SE.
4. If you landed on Team or Pro, the [pricing
   page](/pricing) has the matrix. The
   [Strategic Partner](/partners/inetko) can walk you
   through the onboarding.
5. If you landed on Solo or Starter, go install
   [free forms](/free-forms) and see how far you can
   get on your own. The upgrade ramp is one click.

The framework is the framework. The tier you actually
need is the tier the work demands. Pick it, then commit
to the shape.

::

::marketing-section{band="gray" maxWidth="3xl"}
#default

Want a second opinion on which tier fits? Request
[early access](/getting-started) — we'll route you to a
Strategic Partner SE who's onboarded teams at every
rung.

::
