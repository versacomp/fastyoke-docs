---
title: Trust and verify
summary: What FastYoke records, how the audit chain is sealed, what the platform claims (and explicitly doesn't), and how a third party verifies a sealed envelope offline.
order: 5
---

# Trust and verify

This page covers what the platform records on every signing event,
how the evidence is sealed, how a third party verifies a sealed
envelope independently, and what posture FastYoke takes on the
legal status of those signatures.

## What the platform records

For every signer on every envelope:

- Email of record (the address the invitation was sent to)
- IP address at the time of signing
- User-agent string
- Consent disclosure version and content hash
- Agreed-at timestamp (when consent was acknowledged)
- Signed-at timestamp (when the signature was captured)
- The signature image (PNG)

For the envelope as a whole:

- The source `document_sha256` — a SHA-256 of the exact bytes that
  will be sealed (post-overlay for form-generated PDFs;
  pre-stamping for uploaded PDFs is also recorded)
- An append-only `esign_audit_log` containing every event in
  chronological order with a hash chain
- The signing manifest (described below)
- The ed25519 seal over the manifest

## The Certificate of Completion

When the envelope finalizes, the platform renders a Certificate of
Completion as a Typst-generated PDF. It lists every signer, every
event from the audit log with timestamps, the document
`document_sha256`, the manifest, and the ed25519 signature.

The Certificate is attached to the envelope as a separate file. The
admin and every signer can download it from the public verify page.

## The audit log structure

`esign_audit_log` is append-only at the database layer. Every row
carries:

- The event kind (signer-added, consent-agreed, signature-captured,
  voided, declined, finalized, …)
- A canonical-JSON payload describing the event
- A `row_hash` = SHA-256(prior_row_hash || canonical_payload)

This hash-chaining property means a single tampered row breaks the
chain at every row that follows it. The final row's `row_hash` is
the `chain_head` — the value folded into the manifest before the
seal.

There is no `UPDATE` or `DELETE` path on the audit log table. The
sweep job (`backend/src/esign/sweep.rs`) only inserts rows for
scheduled events; it never mutates existing rows.

## Sealing — what we sign and how

When the last required signer signs, the platform builds a
**canonical-JSON manifest** containing:

- `document_sha256` (hex)
- `chain_head` (hex) — the final audit-log row hash
- `signers[]` — for each signer: email, signed-at timestamp,
  consent disclosure version, IP, user-agent

The manifest is then serialized to canonical JSON exactly once;
the resulting bytes are stored verbatim on the envelope as
`manifest_json`, and **the same bytes** are signed with an
ed25519 key. Verification recomputes the signature against the
literal `manifest_json` bytes — never re-canonicalize, because
any whitespace, key ordering, or escape difference would break
the signature.

The signing key is platform-managed and rotated on a schedule.
Past public keys remain available via the verify-keys endpoint so
older envelopes stay verifiable indefinitely.

## Public verify endpoint

A third party — a court, a buyer's counsel, a compliance system
— verifies a sealed envelope without calling FastYoke at runtime.
The endpoints below are public; no authentication is required.

Fetch the verification record:

```http
GET /api/v1/public/esign/verify/:verification_id
```

Response includes the envelope summary, the list of signers, the
manifest bytes, the ed25519 signature, the audit chain, and the
public-key id used to sign.

To verify the document hash against a copy you have:

```http
POST /api/v1/public/esign/verify/:verification_id
Content-Type: multipart/form-data

<the PDF you want to verify>
```

The platform computes the SHA-256 of the uploaded bytes and
compares against the manifest's `document_sha256`.

Fetch the platform's published ed25519 public keys for offline
verification:

```http
GET /api/v1/public/esign/verify-keys
```

Response is a JSON list of `{ key_id, public_key_base64, valid_from,
valid_to }` records. A verifier reads the response, picks the key
whose id matches the verification record, and checks the
signature.

### Offline-verification recipe (JavaScript)

```js
import nacl from 'tweetnacl';
import { decodeBase64, decodeUTF8 } from 'tweetnacl-util';

async function verifyEnvelope(verificationId, pdfBytes) {
  const v = await fetch(
    `https://verify.fastyoke.io/api/v1/public/esign/verify/${verificationId}`,
  ).then((r) => r.json());

  const keys = await fetch(
    'https://verify.fastyoke.io/api/v1/public/esign/verify-keys',
  ).then((r) => r.json());

  const pk = keys.find((k) => k.key_id === v.key_id).public_key_base64;

  const sigOk = nacl.sign.detached.verify(
    decodeUTF8(v.manifest_json),
    decodeBase64(v.signature_base64),
    decodeBase64(pk),
  );

  const docHash = await crypto.subtle.digest('SHA-256', pdfBytes);
  const docHashHex = Array.from(new Uint8Array(docHash))
    .map((b) => b.toString(16).padStart(2, '0'))
    .join('');

  const docOk = docHashHex === v.document_sha256;

  return { sigOk, docOk };
}
```

The `tweetnacl` library implements ed25519 in pure JavaScript and
works in Node, browsers, and Deno. Any ed25519 implementation
that accepts raw 32-byte public keys and 64-byte signatures will
also work.

## Legal posture

What the platform captures on every signed envelope is **intended to satisfy** the evidence chain commonly accepted for **ESIGN/UETA** in US jurisdictions: a record of the signer's identity, the
signer's intent to sign, the consent disclosure that was shown,
the IP and user-agent at the time of signing, and a tamper-evident
seal over the audit chain. The platform publishes the ed25519
public key so the evidence is independently verifiable.

Identity in the current implementation is **email-control**
baseline — the signer demonstrates control of the email address
the invitation was sent to. This is the same identity baseline
most e-signature platforms ship by default. Stronger factors
(SMS verification, knowledge-based authentication, PKI
certificates) are described in the next section as explicit
non-features today.

Use of FastYoke-sealed documents in a specific legal matter is
for counsel to advise on. The platform produces the evidence; the
sufficiency of that evidence for a particular dispute, in a
particular jurisdiction, against a particular counter-party, is a
legal judgment.

## What's deferred

The following are explicit non-features today. Customers who need
them should plan accordingly until the platform ships them.

- **PAdES** — PDF Advanced Electronic Signatures. The platform's
  ed25519 seal is platform-managed and stored alongside the
  document, not embedded as a PAdES signature in the PDF itself.
- **RFC-3161** external timestamping. Timestamps are platform
  clock; no third-party time-stamping authority is invoked.
- **SMS** identity factor. The signer is not required to receive
  or enter an SMS code to sign.
- **KBA** — knowledge-based authentication. The signer is not
  challenged with identity-verification questions.
- File-level attachment encryption. Envelope attachments are
  stored under the platform's standard encryption-at-rest, not
  with per-envelope wrapping keys.

Customers in regulated environments (some healthcare consent
scenarios, some real-estate jurisdictions) should evaluate
whether the email-control baseline is sufficient for their use
case before adopting.
