---
title: POS
description: A configurable point-of-sale with a product and menu catalog, fast checkout, and card-present payments.
order: 7
---

# POS

POS turns any FastYoke workspace into a point of sale. Build a product and menu
catalog, ring up a sale, and take payment — cash, card-present on a Dejavoo
terminal, or a Vendara pay-by-link — all against one sale record.

## Installing from Logic Cores

1. Go to Logic Cores (sidebar → **Logic Cores**).
2. Find **POS** in the **Yokes** group.
3. Click **Install**. Because POS is a paid listing, clicking Install starts the subscription checkout flow. Complete checkout to activate.
4. Choose whether to load demo data (sample products and sales). Demo data helps you explore the app immediately; you can delete it at any time.
5. Done. The app appears in your workspace sidebar as **POS**.

## Catalog

Add products or menu items with a name, SKU, category, price, and optional
modifier groups (for example, size or add-ons). Mark items inactive to hide them
from checkout without deleting their history.

The **Catalog** workspace lists every active item with search and paging. Use it
to maintain your menu or retail assortment before you open the register.

## Sales

Every sale moves through a simple lifecycle: **Open**, **Held**, **Awaiting
Payment**, **Paid**, and **Refunded**. Hold a sale to start another and resume it
later. Add line items from the catalog; the subtotal, tax, and total update
automatically.

The **Sales** workspace is a board of every sale by state so nothing stalls in
the queue.

## Dashboard

The **Dashboard** is your owner's-eye view of the register. Pick a window —
**Today**, **7 days**, or **30 days** — and see:

- **Key numbers** — total sales, transaction count, average ticket, and refunds.
- **Sales trend** — a day-by-day bar chart so you can spot busy and slow days.
- **Top products** — your best sellers by revenue over the window.

## Sales tax

POS calculates tax per item using configurable **tax classes**. Open the
**Settings** page to manage them:

- Each class has a **name** (for example, `standard`, `food`, `exempt`) and a
  **rate**. One class is the default, used for any item without an explicit
  class.
- Assign a class to each catalog item. When you ring up a sale, every line is
  taxed at its class's rate, and the sale total adds the tax to the subtotal.

Installing with demo data seeds sensible defaults (a standard rate, a reduced
food rate, and a tax-exempt class) that you can edit to match your jurisdiction.

## Checkout

The checkout panel tracks how much has been paid against the total, with a
progress bar and a chip for each tender taken. Take payment three ways, and
combine them on one sale (split tender):

- **Cash** — record a cash amount; the panel shows the **change due** and the
  balance updates instantly.
- **Card (terminal)** — charge a connected Dejavoo terminal in person.
- **Pay by link** — send the customer a secure Vendara payment link.

Use **Exact** to fill the outstanding balance in one tap. A live **receipt**
lists every line item with the subtotal, tax, and total. When the balance is
covered, the sale moves to **Paid** — no refresh needed.

### Refunds

On a paid sale, enter an amount and choose **Refund**. A partial refund leaves
the sale **Paid** with the balance adjusted; refunding the full amount moves it
to **Refunded**. Cash sales reverse without a terminal; card sales refund back
through the Dejavoo terminal.

## Accounting & sync

If you use **Yoke Ledger**, paid sales post to your books automatically (cash
debited, revenue credited). If you connect **QuickBooks Online**, sales sync as
invoices and payments through the standard cross-app bridge.

## Works with your other apps

- **Dejavoo SPIn Payments** — register terminals under Connectors, then charge
  them from the sale checkout block.
- **Vendara Payments** — create pay-by-link invoices for any sale awaiting
  payment.
- **Inventory Yoke** — keep retail stock in sync by linking catalog items to
  inventory SKUs (configure through field linking in your workspace).

## Building on it

All workflows in POS run on FastYoke's FSM Designer. You can extend any lifecycle
without code:

- **Add approval steps** — require a supervisor to confirm a refund before it posts.
- **Add custom fields** — attach table numbers, loyalty IDs, or cost centers via
  **Admin** → **Annotation pages**. New fields appear in forms and list views
  immediately.
- **Customize page layout** — reorder columns, hide fields, or adjust labels in
  **Admin** → **Pages**.

## Removing the app

Uninstalling removes the app's pages and demo records. Records you created after
install are governed by the cleanup settings on each record type's page. The
subscription is cancelled at the end of the current billing period.
