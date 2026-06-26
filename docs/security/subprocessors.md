---
title: Subprocessor List
summary: The third parties FastYoke uses to operate the platform — what each processes, where, and our DPA / compliance posture for each.
order: 3
---

# Subprocessor List

This is the authoritative public list of subprocessors that
process or store FastYoke or tenant data. We update it on
every add, remove, or material change, and notify customers
of new additions per our contractual commitments.

Customers may flag concerns about a subprocessor at
`security@fastyoke.io` before a new addition takes effect.

::callout{type="note"}
**Last updated:** 2026-06-26.
::

## How to read this list

- **Service** — what the subprocessor provides.
- **Data processed** — the data classes FastYoke routes through
  the subprocessor.
- **Region** — primary processing region.
- **DPA / BAA** — whether we have a Data Processing Agreement
  (DPA) on file with the subprocessor. For HIPAA-relevant
  data, a Business Associate Agreement (BAA) is in place
  where indicated.
- **Compliance posture** — material certifications held by the
  subprocessor.

## Active subprocessors

### Infrastructure + hosting

| Subprocessor | Service | Data | Region | DPA | Posture |
|---|---|---|---|---|---|
| **Fly.io** | Application hosting, compute, encrypted volumes, private networking | All production and tenant data at rest and in transit | US (primary), region-pinned where required | Yes | SOC 2 Type II |
| **Cloudflare** | Edge protection, Turnstile anti-abuse, Pages (marketing site hosting) | Request metadata, Turnstile tokens, static web assets | Global edge | Yes | SOC 2, ISO 27001 |
| **GitHub** | Source control, CI/CD, security advisories | Source code, CI logs, secrets-scanning metadata (no tenant data) | US | Yes (Microsoft) | SOC 2, ISO 27001 |
| **S3-compatible object storage** | Continuous DB backup target (Litestream) | Encrypted database backups | US (primary) | Yes | Inherits provider posture |

### Communications + identity

| Subprocessor | Service | Data | Region | DPA | Posture |
|---|---|---|---|---|---|
| **WorkOS** | SSO / AuthKit identity, directory sync | Auth identifiers, SSO profile, organization / directory data | US | Yes | SOC 2 Type II |
| **AWS SES** | Transactional email delivery | Recipient email, message content | US | Yes (AWS DPA) | SOC 2, ISO 27001 |
| **Postmark** | Transactional email (secondary) | Recipient email, message content | US | Yes | SOC 2 |
| **AWS SNS** | OTP SMS delivery (2FA) | Phone number, OTP code | US | Yes (AWS DPA) | SOC 2, ISO 27001 |

### Billing + payments

| Subprocessor | Service | Data | Region | DPA | Posture |
|---|---|---|---|---|---|
| **Stripe** | Billing, subscriptions, Checkout | Billing contact, payment metadata; **FastYoke does not store cardholder data** | US | Yes (Stripe DPA) | PCI DSS Level 1, SOC 2 |

### Deployment integrations

| Subprocessor | Service | Data | Region | DPA | Posture |
|---|---|---|---|---|---|
| **Vercel** | Marketplace SDK + tenant app deploys | Deploy metadata, marketplace integration data | US | Yes | SOC 2 |

### Security tooling

| Subprocessor | Service | Data | Region | DPA | Posture |
|---|---|---|---|---|---|
| **VirusTotal** | File-scan reputation lookup | File hashes (and uploaded samples when configured) | US | Yes | Inherits Google posture |

### LLM providers (optional, opt-in features)

LLM providers process prompt context **only** for tenants who
have opted into AI-assist features (the Advanced App Builder,
Yoker AI assistant). PII is routed through the platform's
**PhiScrubber** by default; tenants may opt out per-call. See
[/docs/security/encryption](/docs/security/encryption) for the
PII redaction contract.

| Subprocessor | Service | Data | Region | DPA | Posture |
|---|---|---|---|---|---|
| **Google (Gemini)** | LLM provider for AI Designer / assist features | Prompt + context; PII scrubbed by default | Multi-region (Google Cloud) | Yes | SOC 2, ISO 27001 |
| **DeepSeek** | LLM provider (default for cost) | Prompt + context; PII scrubbed by default | Multi-region | Subject to review | Tenants with regulated data should switch to Gemini or disable AI-assist features |

## What is NOT a FastYoke subprocessor

The following process tenant data **under the tenant's own
vendor account**. FastYoke is the integration glue and does
not act as a controller's subprocessor for these:

- **Customer-configured webhook receivers.** The platform
  dispatches outbound webhooks to URLs the tenant configures;
  the receiver is the tenant's responsibility.
- **Customer-configured cloud-storage targets.** OneDrive,
  Dropbox, Google Drive — when the tenant connects their own
  account via the Cloud Storage Connector.
- **Customer-configured e-sign envelope recipients.** Signers
  the tenant routes envelopes to are not FastYoke
  subprocessors.
- **Customer-configured accounting exports.** QuickBooks,
  Xero, Sage, Wave — when the tenant exports their GL data
  to their own books.

## Adding or changing a subprocessor

Material changes to this list — adding a new subprocessor, or
changing the data class one processes — are communicated to
customers at least **30 days** before the change takes effect,
per our standard DPA. Customers may flag concerns or request
alternatives at `security@fastyoke.io` during the notice
window.

Removals are announced as completed and do not require
advance notice (a vendor leaving the list is strictly
restrictive).

## See also

- [/docs/security/trust-center](/docs/security/trust-center) —
  the engineer-pitched Trust Center with deeper technical
  controls.
- [/docs/security/encryption](/docs/security/encryption) — the
  PII / SPI Field Encryption add-on.
- [/security](/security) — the executive PII security summary
  for buyers and security committees.
- [/privacy](/privacy) — the platform privacy notice.
