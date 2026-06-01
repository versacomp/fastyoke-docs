---
title: Inventory Yoke
description: Paid marketplace app — track stock across multiple locations with lot and bin tracking, record movements, and get reorder alerts.
order: 4
---

# Inventory Yoke

**Inventory Yoke** is a paid marketplace app that brings structured inventory management to your FastYoke workspace. Track on-hand stock across unlimited locations, record every movement (receiving, transfers, issues, counts, and adjustments), follow items by lot and bin, and get notified when stock drops below a reorder point.

**Pricing:** $49 per month, available on every plan.

## Installing from Logic Cores

1. Go to Logic Cores (sidebar → **Logic Cores**).
2. Find **Inventory Yoke** in the **Yokes** group.
3. Click **Install**. Because Inventory Yoke is a paid listing, clicking Install starts the subscription checkout flow. Complete checkout to activate.
4. Choose whether to load demo data (sample items, locations, and movements). Demo data helps you explore the app immediately; you can delete it at any time.
5. Done. The app appears in your workspace sidebar as **Inventory**.

## What you get

Once installed, you'll see several main workspaces:

| Workspace | What it does |
|---|---|
| **Items** | Your full catalog of stock-keeping units — name, description, unit of measure, reorder point, and reorder quantity. |
| **Locations** | Warehouses, stockrooms, vehicles, job sites — any place where stock lives. Each location can be subdivided into bins. |
| **Receiving** | Guided workflow for inbound stock — log a receiving event, confirm quantities, and post the receipt to inventory. |
| **Transfers** | Move stock between two locations with a two-step confirm-and-complete workflow, so nothing falls through the cracks in transit. |
| **Counts** | Record a physical count for any item and location. Discrepancies are visible before you post the adjustment. |
| **Stock** | Live view of on-hand quantities by item, location, bin, and lot. Use the built-in form to record any movement type — receive, issue, transfer, adjust, or count — directly from this screen. |
| **Reorder Alerts** | A running list of items whose current on-hand quantity has dropped to or below their reorder point. |

## Lot and bin tracking

Items can be tracked by **lot** (a batch identifier that follows product from receipt through issue — useful for expiry dates, recalls, or quality traceability) and by **bin** (a physical location within a larger storage area, such as Aisle 3 / Shelf B). Both are optional per item; you can enable whichever level of precision you need.

## Works with your other apps

Inventory Yoke is designed to connect naturally with other apps in your workspace:

- **CRM Suite** — link inventory items to your product catalog so quotes and orders reference the same items you stock.
- **Field Service** — log parts consumed on a work order directly against inventory. You can also set up a technician's vehicle as a stocked location, making van-stock replenishment part of the dispatch and job-close workflow.

These connections are configured through the standard field-linking tools in your workspace — no code required.

## Building on it

All workflows in Inventory Yoke run on FastYoke's FSM Designer. You can extend any lifecycle without code:

- **Add approval steps** — require a supervisor to confirm a transfer before it posts.
- **Add custom fields** — attach expiry date, supplier reference, cost, or any field you need via **Admin** → **Annotation pages**. New fields appear in forms and list views immediately.
- **Customize page layout** — reorder columns, hide fields, or adjust labels in **Admin** → **Pages**.

## Removing the app

Uninstalling removes the app's pages and demo records. Records you created after install are governed by the cleanup settings on each record type's page. The subscription is cancelled at the end of the current billing period.
