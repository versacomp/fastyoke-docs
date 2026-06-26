---
title: Posting cost of goods sold from inventory
description: Connect your stock movements to the Yoke Ledger — when an issue reduces inventory, FastYoke can automatically post Cost of Goods Sold against Inventory at the item's unit cost, with admin-configurable accounts and an auto-post toggle.
order: 11
---

# Posting cost of goods sold from inventory

When you ship, consume, or otherwise **issue** stock, that inventory leaves your warehouse and turns into an expense — the **cost of goods sold (COGS)**. FastYoke can bridge that moment for you: when an issue movement reduces stock, it posts the matching accounting entry into the **Yoke Ledger** automatically, so your books reflect the cost the instant the stock goes out.

You turn it on under **Settings** in the Inventory workspace.

## When COGS posts

COGS posting happens on exactly one kind of movement: an **issue** that reduces your on-hand quantity. A receipt, a transfer, or any other movement type never triggers a COGS posting.

When an issue movement is recorded and auto-posting is enabled, the Ledger posts a balanced entry valued at the item's **unit cost**:

- **Debit — Cost of Goods Sold** (an Expense account) for *quantity issued × unit cost*
- **Credit — Inventory** (an Asset account) for the same amount

So issuing 4 units of an item that costs $5.00 each posts **$20.00** — a $20.00 debit to your COGS account and a $20.00 credit to your Inventory account. The amount always comes from the item's own unit cost at the time of the issue.

Because the entry is a standard journal posting, it flows straight into your existing reports with no extra setup: the COGS debit shows up as an expense on your **Profit & Loss**, and the Inventory credit reduces the Inventory asset on your **Balance Sheet**.

## Configuring it

Setting up COGS posting is an **admin-only** action.

1. Open **Inventory** and go to the **Settings** tab.
2. Choose your **Cost of Goods Sold account** — the expense account the cost is charged to. Only Expense accounts can be selected here.
3. Choose your **Inventory account** — the asset account stock is credited out of. Only Asset accounts can be selected here.
4. Turn on **Auto-post COGS on issue**.
5. Save.

Auto-posting is **off until you configure it**. You can't enable it without picking both accounts, and each account must be of the right type — an Expense account for COGS and an Asset account for Inventory. If you pick the wrong type, or an account that doesn't exist in your chart of accounts, FastYoke declines the change and tells you why, so a misconfiguration can never start writing crooked entries to your books.

Everyone on your team can **view** the current settings; only an admin can **change** them.

## Posting is best-effort — your stock movement always succeeds

COGS posting is a **best-effort** side effect that runs *after* the stock movement is safely recorded. The movement itself is never blocked by it. If the accounting entry can't be posted for any reason, the stock change still goes through — the posting is simply skipped, and a note is logged for your records.

Posting is skipped (cleanly, with the movement still succeeding) when:

- **Auto-posting is off**, or the accounts haven't been configured yet.
- The item has a **zero unit cost** — there's nothing to value, so no entry is posted.
- The movement falls in a **closed accounting period**. If you've closed your books through a date, a COGS entry that would land in that closed period is skipped rather than forced in — your closed numbers stay put. (See **Closing periods in the Yoke Ledger**.)

In every one of these cases your inventory count is updated exactly as expected; only the optional accounting entry is left out.

## What's covered today

Today this connects the **issue** side of inventory to your books — the cost going *out* as you consume or ship stock. Bringing inventory *in* at cost (the full perpetual-inventory receive side) pairs with vendor bills and is a separate piece of the puzzle.
