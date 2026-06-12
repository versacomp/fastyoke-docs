---
title: Scheduler
description: Fire a transition or another primitive on a cron schedule.
---

# Scheduler

The Scheduler primitive runs an action on a cron-style schedule. Each tick, it can either fire a transition against every job bound to a board, or invoke another primitive (most often [Notifier](/docs/primitives/notifier)) with a fixed config.

## Configuration

| Field | Type | Description |
|---|---|---|
| `trigger.cron` | string | Standard 5- or 6-field cron expression, evaluated in UTC. Validated at save time. |
| `fire.kind` | enum | `transition` or `primitive`. |
| `fire.board` | string | Board name. Required when `fire.kind = "transition"`. |
| `fire.event` | string | FSM event to fire. Required when `fire.kind = "transition"`. |
| `fire.primitive` | string | Slug of the primitive to invoke. Required when `fire.kind = "primitive"`. |
| `fire.config` | object | Config to pass to the named primitive. Required when `fire.kind = "primitive"`. |

## Example: Fire a `send_invoice` event every weekday at 9 AM UTC

```toml
[[bindings]]
primitive = "fastyoke.scheduler"
trigger = { kind = "on_schedule", cron = "0 9 * * 1-5" }
config = {
  fire = { kind = "transition", board = "Subscription", event = "send_invoice" }
}
```

At each tick, Scheduler invokes `apply_transition` against every subscription job bound to the `Subscription` board. Guards still apply — a guard that returns false on a given job means that job is silently skipped.

## Example: Send a daily digest via Notifier

```toml
[[bindings]]
primitive = "fastyoke.scheduler"
trigger = { kind = "on_schedule", cron = "0 8 * * *" }
config = {
  fire = {
    kind = "primitive",
    primitive = "fastyoke.notifier",
    config = {
      channel = "email",
      to = ["ops@example.com"],
      subject = "Daily ops digest",
      body_template = "Today's open tickets: ..."
    }
  }
}
```

## When to use it

- Daily / weekly / hourly recurring transitions (subscription billing, recurring task spawn).
- Periodic notifier dispatch (morning digest, end-of-day summary).
- Polling-like patterns paired with a [Record Spawner](/docs/primitives/record-spawner) or [Field Mutator](/docs/primitives/field-mutator).

## What it can't do

- Replay missed ticks. If the VM is down at the tick time, the tick is skipped — Scheduler will not catch up on restart. For at-least-once guarantees, model the work as a queued entity record instead.
- Sub-minute scheduling. The minimum granularity is the standard cron one-minute resolution.
- Pile up. Each binding fires at most once per tick — overlapping schedules don't double-fire.
