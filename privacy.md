---
title: Privacy Notice — FastYoke
summary: How FastYoke handles personal data submitted through public forms, account info, and product telemetry. Plain-language summary plus the data subject's rights.
order: 3
---

::hero{eyebrow="Privacy" title="Privacy notice" tagline="A plain-language summary of what FastYoke collects, why, and the rights you have over that data."}
::

<section class="mx-auto max-w-3xl px-4 py-8 text-base leading-relaxed text-[var(--brand-text-secondary)]">

**Last updated:** 2026-04-26.

This page is the **scaffold** version of the FastYoke privacy
notice. It covers the platform's defaults; the operator that
embedded the form you're filling out may have layered additional
notices on top. If something here conflicts with a notice that
operator presented to you, the operator's notice prevails for
that interaction.

## TL;DR

- **What we collect:** form submissions you send, account info if
  you create a tenant, and minimal product telemetry needed to
  keep the service running.
- **Why we collect it:** to deliver the service the operator
  configured.
- **What we don't do:** sell your data, run third-party
  ad-targeting trackers, or train models on your form
  submissions.
- **Your rights:** access, correction, deletion, export — email
  [privacy@fastyoke.io](mailto:privacy@fastyoke.io).

## What gets collected

### From public forms

When you submit a form hosted by a FastYoke tenant:

- The exact field values you typed or uploaded.
- File attachments, if the form accepts them. Files are
  scanned for malware via MIME magic + a third-party hash
  check (VirusTotal) before they're delivered to the
  operator.
- The submission timestamp and an opaque internal id.
- A short-lived link between your submission and the public
  invite token the operator gave you, used to gate downstream
  approvals.

We do **not** automatically attach IP address, user-agent,
or device fingerprint to the submission record. Standard
HTTP-server access logs may capture IP for the duration of a
request for abuse prevention; those logs rotate within 30
days and are not joined to submission records.

### From tenant accounts

If you sign in as a tenant administrator or operator:

- Email, hashed password, role assignment, and a session
  token.
- Audit-log entries for actions you take in the admin
  console (workflow edits, job transitions, theme changes).
  These are append-only and survive account deletion as a
  compliance trail.

### From product telemetry

- Aggregated, non-identifying counters: number of API calls
  per tenant per day, transition volume, error rates. Used
  to size infrastructure and bill the tenant; never linked
  back to individual submitters.

## Why we collect it

- **Service delivery.** Every item above is what the
  operator's form / workflow needs to function.
- **Abuse prevention.** Short-window access logs let us
  block credential-stuffing attacks, runaway uploads, and
  malformed-content abuse without retaining identifying
  data long-term.
- **Billing.** The aggregated counters drive transition
  metering on paid tiers. No individual interaction is part
  of the bill.

We do **not** collect data for advertising, profiling, or
secondary commercial use, and we do not run third-party
analytics that fingerprint visitors across sites.

## How long it's kept

| Data | Retention |
|---|---|
| Form submissions | As long as the operator who owns the form keeps them. The operator can delete a submission at any time. |
| Uploaded files | Same as above. Once the parent submission is deleted, attached files are removed within 24 hours. |
| Tenant account info | While the account is active. After deletion, audit-log rows that reference the account stay in the immutable ledger; the account row itself is hard-deleted within 30 days. |
| Access logs | 30 days. |
| Product telemetry | Aggregate counters retained indefinitely; no submitter-level data. |

## Who can see it

- **The tenant operator** who owns the form sees every
  submission to it.
- **FastYoke staff** see your data only when the tenant
  operator opens a support ticket and explicitly grants
  read access for that ticket. Every staff read is logged.
- **No third-party processors** receive submission content
  except the malware scanner (which sees only file
  hashes, never file contents) and our infrastructure
  provider (encrypted at rest and in transit).

## Your rights

If FastYoke has data about you, you can:

- **Access** — get a copy of everything we hold.
- **Correct** — fix anything that's wrong.
- **Delete** — have it removed (subject to the immutable
  audit-log carve-out above).
- **Export** — receive your data in a portable format
  (JSON for structured data, original file for uploads).
- **Object / restrict** — tell us to stop processing.

Email [privacy@fastyoke.io](mailto:privacy@fastyoke.io) and
include enough information that we can locate the record. We
respond within 30 days.

If you're submitting a form, your first stop is usually the
**operator** who hosted the form — they own the data and have
direct delete / correct controls. If they don't respond, we
can route the request on your behalf.

## Cookies and tracking

The marketing site (`/`, `/pricing`, `/docs`, `/privacy`) sets
no cookies. The admin console sets one short-lived
authentication cookie scoped to your session. Public forms
set no cookies at all.

We use no third-party tracking scripts, no advertising
pixels, and no cross-site session reconciliation.

## Children

FastYoke is not designed for users under 16. We do not
knowingly collect data from children. If you believe a
child has submitted a form to a FastYoke-hosted operator,
contact us and we'll work with the operator to remove it.

## Changes to this notice

When this notice changes materially, we update the **Last
updated** date at the top of the page and post a note in the
admin console for the next 30 days. Non-material edits
(grammar, typos, broken links) are made silently.

## Contact

[privacy@fastyoke.io](mailto:privacy@fastyoke.io) — privacy
requests, breach reports, EU/UK representative inquiries.
[security@fastyoke.io](mailto:security@fastyoke.io) — anything
that looks like an active vulnerability.

</section>
