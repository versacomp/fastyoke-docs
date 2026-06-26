---
title: Closing the year in the Yoke Ledger
description: Run a year-end close to sweep your revenue and expense accounts into Retained Earnings and lock the period through the close date — with an admin-only preview, close, and reopen, and no fiscal-year setup to configure.
order: 11
---

# Closing the year in the Yoke Ledger

At the end of a fiscal year, you carry your year's profit (or loss) into equity and start the next year's revenue and expense accounts from zero. The **Yoke Ledger** does this for you with a **year-end close**: a single closing entry, dated the day you choose, that sweeps every revenue and expense balance into a **Retained Earnings** account — then locks the books through that date so the year you just closed stays put.

You'll find it under **Period Close** in the Ledger workspace, alongside the regular period-locking controls.

## What closing the year does

A year-end close posts one balanced **closing entry** dated on your chosen close date. That entry:

- **Zeroes out your revenue and expense accounts** as of the close date — it debits each revenue balance and credits each expense balance so they all return to zero.
- **Moves the net into Retained Earnings.** Your net income for the year is credited to Retained Earnings (a profit), or debited from it (a loss). Retained Earnings is an equity account, so this is where the year's result accumulates from one year to the next.
- **Locks the period through the close date.** Once the close posts, the books are closed through that date and nothing new can be posted into the finished year — exactly like setting a closing date by hand.

On your next Balance Sheet, the closed year's profit now sits in Retained Earnings, and the current-year earnings line starts fresh at zero. Your reports stay correct because the close is a real journal entry, not a display trick.

## No fiscal-year setup needed

There's nothing to configure ahead of time — no "fiscal year start" setting to pick. You simply choose the **close date** (typically the last day of your fiscal year, such as December 31), and the close handles the rest. If your fiscal year doesn't end in December, just pick the date that does.

## A Retained Earnings account is created for you

If your chart of accounts doesn't already have a Retained Earnings account, the Ledger **creates one automatically** (an equity account) the first time you close a year, so the net result has somewhere to land. If you already keep a Retained Earnings account, the close uses it.

## Preview before you close

Closing is an **admin-only** action, and you can always look before you leap.

1. Open the Ledger and go to the **Period Close** tab.
2. In the **Year-End Close** section, pick your **close date**.
3. Click **Preview**.

The preview shows your **net income** for the year and the revenue and expense accounts that will be swept into Retained Earnings — without posting anything. Nothing changes until you confirm the close.

## Closing the year

1. With your close date chosen, click **Close fiscal year**.
2. Confirm in the dialog — it reminds you that this posts a closing entry and locks the books through the date.

The closing entry posts, Retained Earnings picks up the year's net result, and the period locks through your close date. From that point on, no one can post — by hand or automatically — into the closed year.

## Reopening a closed year

Closed too early, or need to record a late adjustment in the year you just closed? An admin can **reopen** it.

1. On the **Period Close** tab, find the close in the list of past closes.
2. Click **Reopen** and confirm.

Reopening **reverses the closing entry** (restoring your revenue and expense balances and backing the net out of Retained Earnings) and **moves the lock back** — to your previous year-end close, or fully open if there wasn't one. Once reopened, you can post into the year again, and re-close it later when you're ready.

## Who can do what

| Action | Who |
|---|---|
| **Preview** a year-end close | Admins only |
| **Close** the fiscal year | Admins only |
| **Reopen** a closed year | Admins only |
| **View** past closes | Admins only |

## How it fits with period close

The year-end close builds on the same closing-date lock used for [closing periods](/docs/apps/yoke-ledger-period-close). A regular period close just sets the lock; a year-end close also posts the closing entry that carries your profit into Retained Earnings. Use a year-end close at the end of your fiscal year, and ordinary period closes for month- and quarter-ends in between.
