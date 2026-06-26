---
title: Invoicing in the Yoke Ledger
description: Author line-item invoices with sales tax, issue them with tax-aware accounting, send them to customers as a PDF, record payments including partial payments, and view an Accounts-Receivable aging report.
order: 9
---

# Invoicing in the Yoke Ledger

The **Yoke Ledger** includes a first-class Accounts-Receivable workspace for billing your customers. Author line-item invoices, issue them so they post to your books automatically, email a PDF to the customer, record payments as they come in (including partial payments), and keep an eye on what's owed with a built-in aging report.

You'll find it under **Invoices** in the Ledger workspace.

## Creating an invoice

1. Open the Ledger and go to the **Invoices** tab.
2. Click **New invoice**.
3. Pick a **customer** (the list is drawn from your existing customer records), or type in a name and email.
4. Set the **issue date**, **due date**, and **payment terms** (for example, *Net 30*).
5. Add **line items** — a description, quantity, and unit price for each. The line amount and the running **subtotal**, **tax**, and **total** update live as you type.
6. Choose a **sales-tax rate** if the invoice is taxable. Tax is calculated on the subtotal at a single rate and rounded to the cent.
7. Click **Save draft**.

A draft is fully editable — change lines, dates, the customer, or the tax rate as many times as you like. Nothing posts to your books until you issue it.

## Issuing an invoice

When the invoice is ready, click **Issue**. Issuing:

- Assigns the next **invoice number** (using the prefix and starting number from your invoice settings).
- Locks the invoice — issued invoices can no longer be edited.
- Posts a **tax-aware journal entry** to your books: it debits Accounts Receivable for the total, credits Revenue for the subtotal, and credits Sales Tax Payable for the tax. Your trial balance stays in balance automatically.

### Posting accounts

Issuing needs to know which ledger accounts to post to. Configure these once under **invoice settings**:

- **Accounts Receivable** — where the amount owed is recorded.
- **Revenue** — where the sale is recorded.
- **Sales Tax Payable** — where collected tax is held (only required when an invoice has tax).
- **Cash / bank** — where payments are deposited.

If an invoice references a tax rate but no Sales Tax Payable account is configured (or the configured account doesn't exist in your chart of accounts), issuing is blocked with a clear message so your books never go out of balance.

## Sending an invoice by email

Once an invoice is issued, open it and click **Email to customer**. The Ledger renders the invoice as a **PDF** and emails it to the customer's address, with the amount due in the message. Every send is recorded in the invoice's delivery log so you can see when — and whether — it went out.

You can also click **Download PDF** at any time to save or print a copy.

::callout{type="info"}
Rendering invoice PDFs and emailing them to customers requires the **Pro plan**. Authoring, issuing, and recording payments are available on every plan.
::

## Recording payments

As customers pay, record each payment against the invoice:

1. Open the issued invoice and click **Record payment**.
2. Enter the **amount** (defaults to the full balance due), the **payment date**, the **method** (cash, check, bank transfer, card, or other), and an optional **reference**.
3. Click **Save**.

Each payment posts its own journal entry — debit Cash/bank, credit Accounts Receivable — and the invoice status updates from its balance:

| Status | Meaning |
|---|---|
| **Issued** | No payments recorded yet. |
| **Partially paid** | Some, but not all, of the balance has been received. |
| **Paid** | The balance is zero. |

**Partial payments** are fully supported — record as many as you need until the balance reaches zero. You can't record a payment larger than the outstanding balance.

Payments form an **append-only history** on the invoice, so the record of what was received, when, and how is always preserved.

## Voiding an invoice

If an invoice was issued in error, open it and click **Void**. Voiding posts a reversing journal entry that cleanly backs out the original posting. An invoice with any recorded payments can't be voided — reverse or refund the payments first.

## The AR aging report

The **Aging** tab shows everything still owed to you, grouped by customer and bucketed by how overdue it is:

- **Current** — not yet due.
- **1–30**, **31–60**, **61–90** — days past due.
- **90+** — over ninety days past due.

Pick an **as-of date** to age the balances against, and the report recalculates. Fully-paid invoices drop out automatically — only outstanding balances appear. You can **export to PDF** or **export to CSV** for sharing or follow-up.

::callout{type="info"}
Exporting the aging report as a PDF requires the **Pro plan**. The on-screen report and CSV export are available on every plan.
::

## Building on it

Everything posts through the standard Yoke Ledger, so your invoices, payments, and tax sit alongside the rest of your books — the trial balance, statements, and reports all reflect AR activity with no extra steps.
