---
title: GL Pack
description: Paid marketplace app — get your business books-ready in one click with a fuller chart of accounts, AR invoices and AP bills that post automatically, opening balances, and starter vendors, customers, and tax codes.
order: 5
---

# GL Pack

**GL Pack** is a paid marketplace app that turns the Yoke Ledger into a working set of business books. Install it once and you get a fuller chart of accounts, accounts-receivable invoices and accounts-payable bills that post to the ledger automatically as they move through their lifecycle, a posted opening-balance entry so your Trial Balance is balanced on day one, and a starter set of vendors, customers, and tax codes to work from.

**Pricing:** available on the **Pro** plan and above.

GL Pack builds directly on the **Yoke Ledger** — the double-entry accounting engine already in your workspace. It installs the Yoke Ledger base first (if it isn't already there), then layers the GL Pack content on top. Everything you post through GL Pack shows up in the Yoke Ledger's Journal, Chart of Accounts, and Reports just like any entry you record by hand.

## Installing from Logic Cores

1. Go to Logic Cores (sidebar → **Logic Cores**).
2. Find **GL Pack** in the **Yokes** group.
3. Click **Install**. GL Pack is a Pro-plan listing, so you'll need to be on the Pro plan or above. If your workspace is on a lower plan, you'll be prompted to upgrade first.
4. Done. The app appears in your workspace sidebar as **GL Pack**.

## What you get

Once installed, you'll see several main workspaces:

| Workspace | What it does |
|---|---|
| **Vendors** | The businesses you buy from. Each vendor carries payment terms and a default expense account so new bills pre-fill correctly. |
| **Customers** | The businesses you sell to. Each customer carries payment terms and a default revenue account so new invoices pre-fill correctly. |
| **Tax Codes** | Reference tax rates you can apply to invoices and bills. Tax codes are reference-only — they record the rate and the payable account; they do not calculate tax for you. |
| **Invoices** | Accounts-receivable invoices on a **Draft → Issued → Paid** workflow, shown as a board grouped by status. |
| **Bills** | Accounts-payable bills on the same **Draft → Issued → Paid** workflow, shown as a board grouped by status. |

## A fuller chart of accounts

GL Pack extends the Yoke Ledger's starter chart of accounts into a complete set covering every account type a small business needs:

- **Assets** — Cash, Accounts Receivable, Inventory.
- **Liabilities** — Accounts Payable, Sales Tax Payable.
- **Equity** — Opening Balance Equity, Retained Earnings.
- **Revenue** — Sales Revenue.
- **Expenses** — Cost of Goods Sold, Rent, Utilities, Office Supplies.

You can rename, add, or deactivate accounts at any time from the Yoke Ledger's Chart of Accounts.

## Invoices and bills that post themselves

The heart of GL Pack is automatic posting. As an invoice or bill moves through its lifecycle, GL Pack writes the matching balanced journal entry to the Yoke Ledger for you — no manual bookkeeping:

| When | Posting |
|---|---|
| An **invoice** is **Issued** | Debit Accounts Receivable, credit Sales Revenue |
| An **invoice** is **Paid** | Debit Cash, credit Accounts Receivable |
| A **bill** is **Issued** | Debit Expense, credit Accounts Payable |
| A **bill** is **Paid** | Debit Accounts Payable, credit Cash |

These are standard FastYoke **posting rules** — you can see them, edit the accounts they hit, or add your own in the Yoke Ledger's Posting Rules workspace.

## Opening balances on day one

GL Pack seeds one posted opening-balance journal entry so your books start from a real position rather than zero. It debits your starting Cash, Accounts Receivable, and Inventory and credits Opening Balance Equity for the total — a balanced entry, so your **Trial Balance balances the moment you install**. Replace the demo figures with your own opening numbers, or void the entry and start fresh, from the Yoke Ledger's Journal.

## Starter reference data

To get you moving immediately, GL Pack seeds a small set of demo records you can edit or delete:

- A couple of **vendors** with payment terms and default expense accounts.
- A couple of **customers** with payment terms and default revenue accounts.
- Two **tax codes** (a standard rate and an exempt rate).
- A handful of sample **invoices** and **bills** spread across the Draft, Issued, and Paid stages so the boards aren't empty.

## Building on it

Every workflow in GL Pack runs on FastYoke's FSM Designer, so you can extend it without code:

- **Add approval steps** — require a reviewer to approve a bill before it can be Issued.
- **Add custom fields** — attach a due date, PO number, or project reference to invoices and bills via **Admin** → **Annotation pages**. New fields appear in forms and list views immediately.
- **Add posting rules** — wire any other lifecycle event to its own balanced journal entry from the Yoke Ledger's Posting Rules workspace.
- **Customize page layout** — reorder columns, hide fields, or adjust labels in **Admin** → **Pages**.

## Removing the app

Uninstalling removes the GL Pack demo records, the invoice and bill posting rules, the opening-balance entry, and the GL Pack app and its pages. The invoice and bill workflows are deactivated rather than deleted, and the Yoke Ledger base — including its chart of accounts — stays in place so any entries you posted are preserved.
