---
title: Privacy Notice — FastYoke
summary: How FastYoke handles personal data submitted through public forms, account info, and product telemetry. Plain-language summary plus the data subject's rights.
order: 3
---

::hero{eyebrow="Privacy" title="Privacy notice" tagline="A plain-language summary of what FastYoke collects, why, and the rights you have over that data."}
::

::marketing-section{band="white" maxWidth="3xl"}

**Last updated:** 2026-05-26.

This page is the **scaffold** version of the FastYoke privacy
notice. It covers the platform's defaults; the operator that
embedded the form you're filling out may have layered additional
notices on top. If something here conflicts with a notice that
operator presented to you, the operator's notice prevails for
that interaction.

> www.fastyoke.io sets zero cookies. [Here's why →](/no-cookies)

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
- (Optional) A verified phone number, if you opt in to SMS
  two-factor authentication. See **SMS messages** below.

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
| Enrolled phone number (SMS 2FA) | While SMS two-factor is enabled on your account. Hard-deleted within 30 days of removal or account deletion. |
| Verification codes (OTP) | 10 minutes from send. Stored only as a salted, peppered SHA-256 hash; never in plaintext. |

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

## Global Privacy Control (Sec-GPC)

If your browser sends the `Sec-GPC: 1` request header —
which Brave, DuckDuckGo, Firefox via an extension, and the
official GPC extension do — FastYoke honors it. We do not
sell or share personal information regardless of the signal;
honoring GPC is the formal acknowledgement that we received
it. We do not engage in behavioral advertising under any
circumstances.

You can verify your browser is sending the signal by visiting
[globalprivacycontrol.org](https://globalprivacycontrol.org).

## Anti-abuse — Cloudflare Turnstile

Public forms may use Cloudflare Turnstile — a CAPTCHA
alternative — to prevent automated abuse. Turnstile collects
browser characteristics (user agent, IP address, and a
non-personalized challenge solve) to determine whether the
visitor is a real human. Turnstile does **not** set tracking
cookies on the marketing site and does not link visits across
unrelated sites. It is classified as a strictly-necessary
security mechanism under the ePrivacy carve-out for service
the visitor explicitly requested (form submission).

Cloudflare's privacy notice covers Turnstile data handling in
detail. See our [subprocessor list](/docs/security/subprocessors)
for the full FastYoke + Cloudflare data-processing posture.

## SMS messages

FastYoke sends SMS text messages only when a tenant
administrator or operator opts in to two-factor authentication
(2FA) on their FastYoke account and enrolls a phone number
through an in-app verification flow.

### What you'll receive

- A 6-digit one-time verification code when you sign in.
- A 6-digit one-time verification code when you first enroll or
  change your phone number.

That is the only category of SMS we send. We do not send
marketing, promotional, recurring, or account-status SMS.
Standard message and data rates may apply from your carrier.

### Frequency

Messages are sent only in response to a sign-in or enrollment
attempt you initiate — typically zero to a few per week per
user. A per-user rate limit caps sends at 4 in any 15-minute
window.

### How to opt in

Open your FastYoke account settings, enable two-factor
authentication, enter your phone number in international format
(E.164, e.g. `+14155551234`), and respond to the verification
code we send. Your number is stored only after you confirm
ownership this way.

### How to opt out

- Reply **STOP**, **UNSUBSCRIBE**, or **CANCEL** to any
  FastYoke verification SMS. Your carrier will block further
  FastYoke SMS to that number; no further action needed on
  your part.
- Or, in your account settings, switch the second factor to
  email (we send the same one-time code to your verified
  account email instead).
- Or, remove your phone number entirely. Two-factor will fall
  back to email.

To re-enable SMS after opting out, re-enroll your phone in
account settings.

### Who sees the phone number and the message

- **FastYoke** stores your phone number in our platform
  database, scoped to your tenant. We use it only to deliver
  verification codes.
- **AWS** is our SMS delivery provider (Amazon SNS). They
  receive the destination phone number and the message body
  at send time, subject to their privacy terms. They do not
  use the content for any other purpose.
- **Your mobile carrier** delivers the message to your
  handset, as with any SMS.
- **No third-party advertising or analytics provider** ever
  receives your phone number or message content.

### How long it's kept

Your enrolled phone number is kept while two-factor SMS is
enabled on your account. Remove it from account settings or
delete your account and it is hard-deleted within 30 days.
Verification codes themselves are short-lived (10-minute
expiry) and are never logged in plaintext.

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

::
