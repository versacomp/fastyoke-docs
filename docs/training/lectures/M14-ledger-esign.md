---
title: Lecture M14 — Yoke Ledger + E-signatures
summary: Why journal entries are immutable once posted, what the seal proves without a PKI, and how the public verify endpoint is the trust model.
order: 15
---

# Lecture M14 — Yoke Ledger + E-signatures

## The mental model

Yoke Ledger is a double-entry GL embedded in the
platform: a Chart of Accounts, Posting Rules that book
transactions, journal entries with a `draft → posted`
lifecycle. E-signatures take a similar shape — a sealed
PDF with a Certificate of Completion that anyone can
verify out-of-band. Both subsystems prize **immutability
once published**.

## Key concepts

- **Chart of Accounts.** A tree of accounts with types
  (asset, liability, equity, revenue, expense). The
  reporting surface aggregates over the tree.
- **Posting Rules.** Map a domain event (e.g.,
  `invoice_posted`) to the journal lines it should write.
  `amount_path` is a JSON Pointer into the event payload.
- **Journal entry lifecycle.** `draft` (editable) →
  `posted` (immutable). Reversal **appends** a mirror
  entry; it never edits history.
- **E-sign seal.** Ed25519 hash-chained signature over
  the PDF + audit log. No CA, no PKI — the seal proves
  the platform serialized this exact PDF at this exact
  time.
- **Certificate of Completion.** The last page of the
  sealed PDF. Carries the public verify URL.
- **Public verify endpoint.** No auth. Anyone with the
  hash can verify. **That's the trust model** — the seal
  is meaningful precisely because the verify endpoint is
  open.
- **Self-loop in posting-rule terms.** A rule with
  `from == to` increments a counter or re-fires a side
  effect without changing the GL position. Same FSM
  self-loop pattern from M6.

## Common pitfalls

- **Trying to edit a posted entry.** It returns 409.
  Reverse and re-post — that's the contract.
- **Re-exporting the sealed PDF.** Different viewers
  reflow whitespace; the seal breaks. Serve the sealed
  copy the platform stores.
- **Hiding the verify endpoint behind auth "for safety."**
  Don't — you destroy the trust model. The seal is
  worthless if only insiders can verify.

## Where to go next

- Lab: [M14 — Ledger & E-sign](/docs/training/labs/M14-ledger-esign).
- Reference: [/docs/yoke-ledger](/docs/yoke-ledger),
  [/docs/esign](/docs/esign).
