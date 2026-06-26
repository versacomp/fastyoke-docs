---
title: Audit trail
summary: The four immutability guarantees, what's signable, and the auditor handoff.
order: 4
---

# Audit trail

## The four immutability guarantees

Posted entries are **immutable**. Reversals are appended.
The event log is append-only. Posting Rules don't rewrite
history. Four rules, all reinforcing the same shape.

1. **Posted journal entries cannot be edited.** Once an
   entry is in `posted` state, no field on it can change.
   The corrective mechanism is reversal, not edit.
2. **Reversals are new entries.** A reversal is a separate
   journal entry that mirrors the original (debits and
   credits swapped). The original posted entry survives
   in the GL alongside the reversal.
3. **The event log is append-only.** Every state change
   on every entry writes an event-log row. Rows cannot
   be deleted or edited. The log is the platform's
   audit chain.
4. **Posting Rules cannot retroactively change posted
   entries.** Editing a rule today does not re-run it
   against historical events. A rule change applies to
   future firings only.

## What's signable

The General Ledger detail report can be exported as a
**sealed PDF** via the platform's e-sign trust model —
the same ed25519 seal that the e-sign module uses for
contracts. The seal proves the exact GL state at the
moment of export. See [E-signatures](/docs/esign) for
the trust model.

A sealed GL export is what you hand to an external
auditor when they ask for "the books as of [date]." The
seal is verifiable by any third party with the public
verify endpoint — no FastYoke involvement required.

## The never-delete-the-books guarantee

Uninstalling Yoke Ledger from the Marketplace does NOT
delete:

- Journal entries.
- Chart of Accounts.
- Posting Rules history.
- Event log.

The Yoke Ledger app surface goes away. The data
underneath stays. (Cross-reference: [Yoke Ledger
overview](/docs/yoke-ledger).)

## Auditor handoff — three exports

When an external auditor (or your firm's review partner)
arrives:

1. **Trial Balance** for the audit period.
2. **General Ledger detail** for the audit period, as a
   sealed PDF.
3. **Chart of Accounts** snapshot — current state of
   every account.

Each is one click from the Reports surface. The sealed
PDF is what makes the package defensible.

## What's NOT preserved

**Voided/deleted draft entries.** Drafts that were never
posted leave no audit trail. This is intentional —
drafts are working state, not records of business
events. If you need a record of "the proposed entry we
decided not to post," create the entry, post it, then
immediately reverse it. Both posted entries survive in
the GL.
