---
title: Handle email bounces and complaints
summary: How FastYoke processes AWS SES bounce and complaint notifications, where the suppression list lives, and how to recover a recipient.
order: 20
---

# Handle email bounces and complaints

FastYoke receives delivery feedback from AWS SES through an SNS HTTPS subscription
wired to `POST /internal/ses/feedback`. The handler verifies the AWS RSA signature,
classifies the event, appends an immutable row to `email_deliveries`, and — for hard
bounces and complaints — also inserts an `email_suppressions` row and mirrors the
address to the SES account-level suppression list so future sends are blocked at both
layers.

## How the feedback loop works

```
SES event (bounce / complaint / delivery)
  → SNS topic (fastyoke-email-feedback)
    → HTTPS POST /internal/ses/feedback
      → signature verification
        → dispatch_feedback()
          ├── email_deliveries (every event, append-only)
          └── email_suppressions (hard bounce + complaint only)
              └── SES PutSuppressedDestination (best-effort mirror)
```

Events that trigger suppression:

| SES event type | Bounce type | Suppression? | `email_deliveries.state` |
|---|---|---|---|
| Bounce | Permanent | Yes — `bounce_hard` | `bounced` |
| Bounce | Transient | No | `bounced` |
| Complaint | — | Yes — `complaint` | `complained` |
| Delivery | — | No | `delivered` |
| Reject | — | No | `rejected` |
| RenderingFailure | — | No | `rendering_failed` |

## Operator setup

Before this handler receives real SNS traffic, complete three steps in AWS:

1. **Create (or update) an SES configuration set** — name it `fastyoke-prod-feedback-eventdest`.
   Enable the following event destinations publishing to SNS:
   `Bounce`, `Complaint`, `Delivery`, `Reject`, `RenderingFailure`.
2. **Create an SNS HTTPS subscription** on the `fastyoke-email-feedback` topic pointing
   to `https://<your-host>/internal/ses/feedback`.
3. **Watch for the SubscriptionConfirmation handshake** — FastYoke GETs the
   `SubscribeURL` automatically when SNS POSTs a `SubscriptionConfirmation` message.
   Look for the log line:

   ```
   INFO SES SNS SubscriptionConfirmation: GET'ing SubscribeURL (audit)
   ```

   That line appearing confirms the subscription is active and SNS will start delivering
   events.

::callout{type="tip" title="Tag your messages with tenant_id"}
FastYoke resolves the tenant scope of a suppression from a `tenant_id` SES message tag.
If your send path sets `{ "tenant_id": ["t-acme"] }` as a message tag, the suppression
row is scoped to that tenant only. Messages sent without a `tenant_id` tag (for example,
system-level mail like account alerts) result in a platform-wide suppression row
(`tenant_id IS NULL`) that protects the shared SES account reputation across all tenants.
::

## Signature verification

Every SNS POST is verified before any DB write runs. The verification steps are:

1. The `SigningCertURL` in the envelope is checked against an allowlist — only URLs
   whose host matches `*.amazonaws.com` are accepted. Requests with a cert URL from any
   other host are rejected with `403` immediately (no network request, no DB work).
2. The cert is fetched (or served from a 24-hour in-process cache) and its
   `notBefore`/`notAfter` validity window is checked.
3. The RSA-PKCS1 v1.5 signature in the envelope is verified against the AWS canonical
   string. Both SignatureVersion `1` (SHA-1) and `2` (SHA-256) are supported.

Invalid signatures return `403`. The status codes SNS receives are intentional:

| Status | Meaning | SNS retry? |
|---|---|---|
| `400` | Envelope or inner JSON parse failure | No |
| `403` | Cert URL not allowed, or signature mismatch | No |
| `500` | Database write failed | Yes (SNS retries) |
| `502` | Cert fetch or SubscribeURL GET failed | Yes (SNS retries) |

## Where suppression data is stored

### `email_suppressions`

An append-only table. Each row records one suppression event. Rows are never deleted;
to remove a suppression, a "supersession" row is written pointing back at the original
via `superseded_by_id`, and the active read path filters `WHERE superseded_at IS NULL`.

Key columns:

| Column | Type | Notes |
|---|---|---|
| `tenant_id` | `TEXT` (nullable FK) | `NULL` = platform-wide suppression |
| `recipient_email` | `TEXT NOT NULL` | The address being suppressed |
| `reason` | `TEXT NOT NULL` | One of `bounce_hard`, `bounce_soft`, `complaint`, `unsubscribe`, `manual`, `admin_block` |
| `template_class_scope` | `TEXT` (nullable) | If set, suppression is scoped to a single email class |
| `source_message_id` | `TEXT` (nullable) | SES message ID that triggered the suppression |
| `created_at` | `INTEGER NOT NULL` | Unix epoch milliseconds |
| `superseded_at` | `INTEGER` (nullable) | Set when a later record lifts the suppression |

### `email_deliveries`

An append-only ledger. Every feedback event (including soft bounces and deliveries)
appends a row here regardless of whether a suppression row is also written. Multiple
rows may exist for the same `ses_message_id` as events arrive over time; reads pick
the latest by `last_event_at`.

## SES account-level suppression mirror

After writing a local `email_suppressions` row, FastYoke calls
`PutSuppressedDestination` on the SES account-level suppression list. This is
**best-effort**: if the AWS API call fails, a warning is logged but the local row
stands and the handler returns `200` to SNS. The local row is the source of truth;
the SES mirror ensures that even if the outbox worker bypasses the local check for
any reason, SES itself will block delivery.

::callout{type="warn" title="SES account-level list is shared"}
`PutSuppressedDestination` operates at the AWS account level — it affects all
SES sends from this account, not just FastYoke's. If you share the SES account
with other senders, a hard bounce recorded by FastYoke will suppress that address
account-wide. Use a dedicated SES account per environment (production, staging)
to keep suppression lists isolated.
::

## Recovering a suppressed address

A suppression row can be lifted by writing a new row that sets `superseded_at` on the
original (supersession pattern). There is no admin UI for this yet — it requires a
direct database operation or a support workflow. To verify whether an address is
currently suppressed, query:

```sql
SELECT id, tenant_id, reason, created_at
FROM email_suppressions
WHERE recipient_email = 'user@example.com'
  AND superseded_at IS NULL;
```

An empty result means the address is not suppressed locally. You should also check the
SES account-level suppression list in the AWS console under
**SES → Suppression list** to confirm the mirror was removed there as well.

::callout{type="info" title="Self-service suppression removal"}
An admin UI surface for listing and lifting suppressions is planned for a future
release. Until then, suppression removal requires direct DB access or a platform
operator action.
::

## Verifying the integration end-to-end

AWS provides mailbox simulator addresses you can use without sending to real recipients:

1. **Hard bounce** — send to `bounce@simulator.amazonses.com`. Expect:
   - An `email_suppressions` row with `reason = 'bounce_hard'`.
   - An `email_deliveries` row with `state = 'bounced'`.
   - A `PutSuppressedDestination` call logged on the SES account.
2. **Complaint** — send to `complaint@simulator.amazonses.com`. Expect:
   - An `email_suppressions` row with `reason = 'complaint'`.
   - An `email_deliveries` row with `state = 'complained'`.
3. **Delivery** — send to `success@simulator.amazonses.com`. Expect:
   - An `email_deliveries` row with `state = 'delivered'`.
   - No `email_suppressions` row.

After each simulator send, query `email_deliveries` and `email_suppressions` in the
platform database to confirm rows appear.

## Related

- [SMTP Integration](/docs/integrations) — configure which SMTP credentials FastYoke
  uses to send outbound mail.
- [Security: encryption](/docs/security/encryption) — encryption-at-rest model
  for sensitive payloads.
