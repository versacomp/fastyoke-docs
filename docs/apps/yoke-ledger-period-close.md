---
title: Closing periods in the Yoke Ledger
description: Close your books through a date so no one can post — manually or automatically — into a finished accounting period, with admin-only close and reopen controls and a full audit of every change.
order: 10
---

# Closing periods in the Yoke Ledger

Once you've finished an accounting period — a month, a quarter, or a year — you usually want to **close the books** so that nothing new lands in numbers you've already reviewed, reported, or filed taxes on. The **Yoke Ledger** lets you set a closing date: a watermark that says *"the books are closed through this date."* After that, the Ledger refuses to post any journal entry dated on or before it.

You'll find it under **Period Close** in the Ledger workspace.

## What closing the books does

Closing sets a single closing date for your tenant. From then on, the Ledger will **not post** any journal entry dated on or before that date. If someone tries, the post is rejected with a clear message explaining that the period is closed — and nothing is written to your books.

The closing date is **inclusive**: closing through *June 30* blocks posts dated June 30 and earlier, while July 1 and later remain open.

This applies to **everything that posts to the ledger**, not just one path:

- **Manual journal entries** you create and post by hand.
- **Auto-posted entries** the Ledger generates for you — issuing an invoice, recording a payment, voiding an invoice, and any other activity that posts to your books automatically.

So once a period is closed, neither a hand-keyed entry nor a back-dated invoice payment can slip into it. Your closed numbers stay exactly as you left them.

## Drafts are still fine

Closing a period only blocks **posting**, not authoring. You — and anyone on your team — can still:

- Create and edit **draft** journal entries dated in a closed period.
- Author and edit **draft** invoices with dates in a closed period.

A draft simply can't be **posted** into the closed period. If you need to record something dated in a closed period, you'll either need to date it after the closing date or have an admin reopen the period first (see below).

## Closing a period

Closing is an **admin-only** action.

1. Open the Ledger and go to the **Period Close** tab.
2. Pick the **closing date** — the last day you want included in the closed period.
3. Add an optional **note** (for example, *"June month-end close"*) for the record.
4. Click **Close through this date**.

The status updates to show the books are closed through your chosen date, and from that moment posting into the period is blocked.

## Reopening a period

Made the close too early, or need to record a late entry? An admin can **reopen** by moving the closing date earlier — or clearing it entirely to reopen everything.

1. On the **Period Close** tab, set an earlier closing date, or choose **Reopen / clear**.
2. Confirm the change.

Once reopened, posting into the now-open dates works normally again. You can re-close later when you're done.

## Who can do what

| Action | Who |
|---|---|
| **View** the current closing date and history | Everyone on your team |
| **Close** a period (set or advance the closing date) | Admins only |
| **Reopen** a period (move the date earlier or clear it) | Admins only |

A non-admin who tries to close or reopen is turned away — only the controls they're allowed to use are shown.

## Audit history

Every close and reopen is recorded in an **append-only audit log**. Each entry captures what changed (the previous and new closing dates), whether it was a **close** or a **reopen**, who made the change, when, and any note they left. The most recent changes appear right on the **Period Close** tab, so there's always a clear record of how your closing date got to where it is.

## How it fits with reports

Closing a period changes only what can be **posted** — it doesn't change what you can **see**. All of your statements and date-ranged reports keep working exactly as before, across both open and closed periods. Closing simply guarantees that the figures inside a closed period won't move out from under a report you've already run.
