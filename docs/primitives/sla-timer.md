---
title: SLA Timer
description: Escalate a job after it has been in a given state for too long.
---

# SLA Timer

The SLA Timer primitive arms a deadline when a job enters a state, then fires an escalation action — either a transition or another primitive — if the job is still in that state when the deadline elapses. If the job moves on before the timer fires, the timer is silently cleared.

## Configuration

| Field | Type | Description |
|---|---|---|
| `trigger.board` | string | Board name. |
| `trigger.state` | string | The state whose entry arms the timer. |
| `after` | ISO-8601 duration | How long to wait (e.g. `PT4H` for four hours, `P1D` for one day). Minimum one minute, maximum 90 days. |
| `escalate.kind` | enum | `transition` or `primitive`. |
| `escalate.event` | string | FSM event to fire on escalation. Required when `escalate.kind = "transition"`. |
| `escalate.primitive` | string | Slug of the primitive to invoke. Required when `escalate.kind = "primitive"`. |
| `escalate.config` | object | Config to pass to the named primitive. Required when `escalate.kind = "primitive"`. |

## Example: Auto-escalate Tickets that sit in Open for 4 hours

```toml
[[bindings]]
primitive = "fastyoke.sla-timer"
trigger = { kind = "on_state_entry", board = "Ticket", state = "Open" }
config = {
  after = "PT4H",
  escalate = { kind = "transition", event = "auto_escalate" }
}
```

When a Ticket enters `Open`, an `__sla_armed__` marker is appended to the event log with the deadline. At each cron tick, the engine checks for armed markers whose deadline has passed *and* whose job is still in the armed state. Those jobs receive the `auto_escalate` event. If a Ticket moves out of `Open` before the four hours elapse, the marker is silently cleared.

## When to use it

- Customer-support SLAs (escalate to a senior agent after N hours).
- Dispatch waiting on driver acceptance (fall back to the next driver after N minutes).
- Approval workflows that auto-deny if a quorum doesn't sign off within N days.

## What it can't do

- Sub-minute precision. The minimum duration is one minute; ticks run on the same cadence as [Scheduler](/docs/primitives/scheduler).
- Multi-step escalation chains in a single binding. Use two SLA Timers, the second armed by the state your first timer transitions into.
- Replay missed ticks if the VM was down at the exact deadline. The next tick after the VM is back catches up.
