---
title: Lab M14 — Solution
summary: Answer key for the M14 ledger & e-sign lab — exact GL curls, the reversal contract, the public verify endpoint shape, and three sealed-PDF gotchas.
order: 29
---

# Lab M14 — Solution

> **Reveal warning.** This page is the **answer key** for
> [Lab M14 — Ledger & E-sign](/docs/training/labs/M14-ledger-esign).
> Try each step on your own first; the friction is where the
> learning lives.

## Step 4 — Post response shape

```json
{
  "id": "je_abc123",
  "state": "posted",
  "posted_at": "2026-06-22T18:30:00Z",
  "memo": "M14 lab — sample invoice",
  "lines": [
    { "account": "4000", "side": "credit", "amount_cents": 10000 }
  ]
}
```

## Step 5 — Reversal contract

A reversal **never edits the original**. The platform:

1. Leaves the original `posted` entry untouched.
2. Appends a new entry with the same lines, sides
   mirrored.
3. Links them via a `reversed_by` / `reverses` pair so the
   trial balance nets to zero.

Audit log retains both rows — auditors verify the chain.

## Step 8 — Public verify endpoint

```bash
curl -i "https://www.fastyoke.io/api/v1/public/esign/verify?hash=$HASH"
```

Response:

```json
{
  "envelope_id": "env_abc",
  "hash": "sha256:...",
  "sealed_at": "2026-06-22T18:45:00Z",
  "signers": [
    {
      "email": "you@example.com",
      "signed_at": "2026-06-22T18:42:00Z",
      "ip": "203.0.113.1"
    }
  ],
  "certificate_url": "https://www.fastyoke.io/api/v1/public/esign/certificate/..."
}
```

No auth required — the public endpoint IS the trust model.
Anyone with the hash can verify the seal.

## Three gotchas

- **Self-loop in posting-rule terms.** A rule with
  `from == to` increments a counter or re-fires a side
  effect without changing the GL position — same FSM
  self-loop pattern from M6. Useful for retry counters.
- **Sealed-PDF re-hash.** If you re-export the PDF from a
  different viewer (preview, ghostscript), whitespace may
  reflow and break the seal. Always serve the sealed copy
  the platform stores; never re-render.
- **Verify endpoint is public.** That is the entire trust
  model. The seal is meaningful precisely because anyone
  can verify it without a session.

## Back to the lab

Return to [Lab M14 — Ledger & E-sign](/docs/training/labs/M14-ledger-esign).
