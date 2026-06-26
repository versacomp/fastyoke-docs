---
title: Bill payments in the Yoke Ledger
description: Enter vendor bills with per-line expense or inventory accounts and sales tax, approve them with tax-aware accounting that posts the expense and the payable (and receives inventory into stock), record payments including partial payments, and view an Accounts-Payable aging report by vendor.
order: 12
---

# Bill payments in the Yoke Ledger

The **Yoke Ledger** includes a first-class Accounts-Payable workspace for the money you owe your vendors — the payables mirror of invoicing. Enter vendor bills line by line, approve them so they post to your books automatically, receive inventory-backed lines straight into stock, record payments as you make them (including partial payments), and keep an eye on what you owe with a built-in aging report.

You'll find it under **Bills** in the Ledger workspace.

## Entering a bill

1. Open the Ledger and go to the **Bills** tab.
2. Click **New bill**.
3. Pick a **vendor** (the list is drawn from your existing vendor records), or type in a name.
4. Set the **receive date**, **due date**, and **payment terms** (for example, *Net 30*).
5. Add **line items** — a description, quantity, and unit price for each. The line amount and the running **subtotal**, **tax**, and **total** update live as you type.
6. Choose the **account** each line posts to. An expense line points at an expense account; an **inventory** line points at your Inventory asset account and also names the **item** and the **location** it's received into.
7. Choose a **sales-tax rate** if the bill is taxable. Tax is calculated on the subtotal at a single rate and rounded to the cent.
8. Click **Save draft**.

A draft is fully editable — change lines, dates, the vendor, the per-line accounts, or the tax rate as many times as you like. Nothing posts to your books until you approve it.

## Approving a bill

When the bill is ready, click **Approve**. Approving:

- Assigns the next **bill number** (using the prefix and starting number from your bill settings).
- Locks the bill — approved bills can no longer be edited.
- Posts a **tax-aware journal entry** to your books: it debits each line's account (expenses and inventory), debits Sales Tax Payable for any tax, and credits Accounts Payable for the total. Your trial balance stays in balance automatically.
- For every **inventory-backed line**, receives the quantity into stock at the named location — so your on-hand counts reflect the goods as soon as the bill is approved.

### Posting accounts

Approving needs to know which ledger accounts to post to. Configure these once under **bill settings**:

- **Accounts Payable** — where the amount owed is recorded.
- **Expense** — the default account for non-inventory lines.
- **Inventory** — the default account for inventory-backed lines.
- **Sales Tax Payable** — where tax owed is held (only required when a bill has tax).
- **Cash / bank** — where payments are drawn from.

Each line can override its account, falling back to these defaults. An inventory line must name both an item and a location, so approving is blocked with a clear message if either is missing — your stock and your books never drift apart.

## Recording payments

As you pay your vendors, record each payment against the bill:

1. Open the approved bill and click **Record payment**.
2. Enter the **amount** (defaults to the full balance due), the **payment date**, the **method** (cash, check, bank transfer, card, or other), and an optional **reference**.
3. Click **Save**.

Each payment posts its own journal entry — debit Accounts Payable, credit Cash/bank — and the bill status updates from its balance:

| Status | Meaning |
|---|---|
| **Approved** | No payments recorded yet. |
| **Partially paid** | Some, but not all, of the balance has been paid. |
| **Paid** | The balance is zero. |

**Partial payments** are fully supported — record as many as you need until the balance reaches zero. You can't record a payment larger than the outstanding balance.

Payments form an **append-only history** on the bill, so the record of what was paid, when, and how is always preserved.

## Voiding a bill

If a bill was approved in error, open it and click **Void**. Voiding posts a reversing journal entry that cleanly backs out the original posting, and backs the received quantity out of stock for any inventory lines. A bill with any recorded payments can't be voided — reverse the payments first.

## The AP aging report

The **Bill Aging** tab shows everything you still owe, grouped by vendor and bucketed by how overdue it is:

- **Current** — not yet due.
- **1–30**, **31–60**, **61–90** — days past due.
- **90+** — over ninety days past due.

Pick an **as-of date** to age the balances against, and the report recalculates. Fully-paid bills drop out automatically — only outstanding balances appear. You can **export to PDF** or **export to CSV** for sharing or follow-up.

::callout{type="info"}
Exporting the aging report as a PDF requires the **Pro plan**. The on-screen report and CSV export are available on every plan.
::

## Building on it

Everything posts through the standard Yoke Ledger, so your bills, payments, and tax sit alongside the rest of your books — the trial balance, the Profit &amp; Loss statement, and the Balance Sheet all reflect Accounts-Payable and inventory activity with no extra steps.
