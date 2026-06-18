---
title: PII / SPI Field Encryption
summary: Tag entity-field annotations or form-field schemas with `is_pii` to encrypt their values at rest under a per-tenant key. Available as a $19/mo add-on at every tier.
---

# PII / SPI Field Encryption

The encryption add-on encrypts tenant-tagged sensitive fields at
rest in FastYoke's databases. Tagging is opt-in and per-field —
non-tagged data continues to be stored as plain JSON.

::callout{type="info"}
Looking for **roles and permissions**? See [Permissions](/docs/permissions).
::

## What it covers

| Surface | Behavior |
|---|---|
| Entity records | Tagged fields encrypt on create/update; decrypt on read. Filter / sort against a tagged field is refused with a 422 — the values are intentionally opaque to search. |
| Form submissions | Tagged fields in the form's schema encrypt at submit. Admins viewing the submission see plaintext. Generated PDFs include the plaintext value. |
| Job audit logs | Snapshot bytes stored in the audit log (FSM transitions) decrypt at read time. The on-disk audit ledger remains append-only. |
| File attachments | Files uploaded into a `is_pii: true` file field land on disk wrapped in an authenticated-encryption envelope. Admin downloads and the PDF render decrypt automatically. |

This is **not**:

- Disk- or volume-level encryption (Fly volumes already do that
  at the infrastructure layer; this add-on is application-layer).
- Customer-managed keys (BYOK) — every tenant uses a per-tenant
  key wrapped under FastYoke's platform key.

## How it works

Two layers of keys:

1. **Platform key (KEK)** — a single 32-byte key in FastYoke's
   environment. Used only to wrap per-tenant keys; never touches
   user data directly.
2. **Per-tenant data key (DEK)** — generated when you subscribe.
   Stored wrapped under the platform key. Unwrapped in memory
   per-tenant when needed.

Each encrypted value carries the key version it was encrypted
under, so rotating the key doesn't invalidate older data.

## Tagging a field

### On an entity field

Open `Admin → Entities → :name → Annotations`. The Sensitive
(PII) checkbox sits next to the Required toggle. Tagging an
existing field encrypts on the **next** save — values written
before tagging stay plaintext until you run a backfill
(`Settings → Encryption → Backfill`).

### On a form field

In the Forms Builder field panel, toggle **Sensitive (PII)**.
Submissions land encrypted from the moment the toggle is on.
Existing submissions need a backfill.

## Subscribing

The add-on is $19/mo at every commercial tier. Subscribe from
**Settings → Marketplace add-ons → Encryption** (Stripe
Checkout). Cancellation goes through the same dashboard.

If you toggle a field as Sensitive without an active
subscription, the toggle is stored, but values continue to be
written as plaintext until the add-on is reinstated. The
admin field-config UI shows an amber banner whenever the
add-on is inactive.

## What happens on lapse

If the subscription lapses but encrypted data already exists,
read access continues to work — historical ciphertext stays
decryptable under the still-resident key. New writes are
accepted as plaintext (the toggle is treated as inert) so
your admin dashboards don't break. Re-subscribing resumes
encrypt-on-write.

To **fully reclaim plaintext** and remove the encryption layer,
use **Disable and decrypt** (see below).

## Rotating keys

`Settings → Encryption → Rotate key` mints a new tenant key
version. Existing encrypted values stay readable — the key
version is stamped on every encrypted value. New writes use the
new version. Rotation is manual; FastYoke does not auto-rotate.

## Disabling encryption ("Disable and decrypt")

This walks every encrypted row across entities, forms, jobs,
and files; decrypts under the active key; rewrites plaintext;
then shreds the tenant key. After completion the toggle states
remain on your fields — re-subscribing does not auto-re-encrypt
existing rows; you'd run a backfill manually. Cancellation of
the underlying subscription is required (currently via the
Stripe dashboard).

This action requires typing your tenant name to confirm.

## What's encrypted vs visible to FastYoke staff

Your DEK is wrapped under FastYoke's platform key — staff with
access to the production environment can in principle decrypt
your data. This is the same trust boundary as every other field
in the FastYoke platform today; the encryption add-on raises
the bar against database-only access (a leaked DB dump alone
cannot reveal tagged values without the platform key).

For a stronger guarantee — bring-your-own-key (BYOK) — see the
roadmap.

## Limitations

- **No search on tagged fields.** A query against an encrypted
  field returns 422; you'll need to refactor the search to
  resolve via id/foreign key.
- **No deterministic encryption / blind index.** Two tagged
  values that look identical to the user produce different
  ciphertexts. Equality joins inside SQLite over a tagged
  column don't work.
- **Audit ledger and event_log are not encrypted.** Don't put
  PII in event_log entries; the platform's append-only audit
  contract intentionally keeps that table plaintext for
  forensics.

## API surface

- `POST /api/v1/tenant/billing/checkout/addon?addon=pii_encryption`
- `POST /api/v1/tenant/encryption/rotate`
- `GET  /api/v1/tenant/encryption/status`
- `POST /api/v1/tenant/encryption/backfill?surface={entities|forms|jobs|files}`
- `POST /api/v1/tenant/encryption/offboard`
- `GET  /api/v1/tenant/encryption/offboarding/status`

All endpoints require a tenant JWT and the
`tenant.encryption.manage` permission (defaults to admins) for
write actions. `read_status` is read by anyone with billing
read access.
