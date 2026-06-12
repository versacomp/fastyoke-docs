---
title: Approval Gate
description: Block a transition until N approvers with the right roles have voted yes.
---

# Approval Gate

The Approval Gate primitive intercepts a transition request, writes a pending-approval marker to the event log, and waits until enough approver votes arrive before letting the underlying transition fire. The job's state stays put while a request is pending; the transition runs exactly once when the threshold is met.

## Configuration

| Field | Type | Description |
|---|---|---|
| `trigger.board` | string | Board name. |
| `trigger.event` | string | The transition event to gate. |
| `required_approvers` | integer | Number of distinct voters who must say yes (e.g. `2`). |
| `approver_roles` | string[] | Roles that count as approvers (e.g. `["admin", "manager"]`). |
| `max_approvers` | integer | Optional. Caps the number of voters allowed; defaults to `required_approvers`. |

## Example: Expense approval requires two managers

```toml
[[bindings]]
primitive = "fastyoke.approval-gate"
trigger = { kind = "on_transition", board = "Expense", event = "approve" }
config = {
  required_approvers = 2,
  approver_roles = ["admin", "manager"]
}
```

The first time a user requests the `approve` event on an Expense, the engine writes an `__awaiting_approval__` row to the event log and returns without changing state. Subsequent calls by other users vote on that pending request via the approval vote endpoints. When two distinct admins or managers vote yes, the `approve` transition fires.

## When to use it

- Expense or PO approvals that require multiple sign-offs.
- Refund / credit issuance gated on a manager's review.
- Any "two-key" workflow where one operator shouldn't be able to single-handedly advance a job.

## What it can't do

- Allow the requester to also vote. The user who requested the transition is excluded from the voter count.
- Time out automatically. Pair Approval Gate with an [SLA Timer](/docs/primitives/sla-timer) bound to the same state to auto-deny or escalate stale requests.
- Combine "anyone who hasn't seen this" voting with quorum logic. V1 is plain N-of-M with role filtering.
