---
title: Notifier
description: Send an email or fire a webhook on transitions, state changes, record creations, or field updates.
---

# Notifier

The Notifier primitive fires an outbound message when one of your Yoke's triggers matches. It supports two channels — email (free) and webhook (Pro) — and uses Mustache-style `{{path}}` substitution against the entity's payload to template the subject, body, and recipient list.

## Configuration

| Field | Type | Description |
|---|---|---|
| `channel` | enum | `email` or `webhook`. |
| `to` | string[] | Email recipient list. Each entry may be a literal address or a `{{path}}` reference into the entity payload. Used when `channel = "email"`. |
| `subject` | string | Email subject template. Used when `channel = "email"`. |
| `body_template` | string | Message body template. Used for both channels. |
| `webhook_url` | string | Destination URL when `channel = "webhook"`. Must be `https://` and pre-allowlisted in your tenant settings. |
| `webhook_secret` | string | Optional shared secret used to HMAC-sign the webhook body. |

`{{path}}` references resolve against the entity's `data_payload` for transition and state triggers, against the changed record for record-create or field-change triggers.

## Example: Email the customer when an Order is fulfilled

```toml
[[bindings]]
primitive = "fastyoke.notifier"
trigger = { kind = "on_transition", board = "Order", event = "fulfill" }
config = {
  channel = "email",
  to = ["{{owner_email}}"],
  subject = "Order {{order_number}} fulfilled",
  body_template = "Order {{order_number}} for {{customer_name}} has been fulfilled."
}
```

## Example: Webhook on every state change of a Ticket

```toml
[[bindings]]
primitive = "fastyoke.notifier"
trigger = { kind = "on_state_entry", board = "Ticket", state = "Resolved" }
config = {
  channel = "webhook",
  webhook_url = "https://hooks.example.com/yoke/ticket-resolved",
  webhook_secret = "<your-shared-secret>",
  body_template = "{\"ticket_id\": \"{{id}}\", \"customer\": \"{{customer_name}}\"}"
}
```

## When to use it

- Customer-facing email on lifecycle events (order shipped, quote sent, appointment confirmed).
- Operator alerts on edge cases that don't warrant a full FSM transition.
- Outbound webhook bridging to a third-party system that isn't a first-class connector yet.

## What it can't do

- Render a PDF attachment — use [PDF Generator](/docs/primitives/pdf-generator) and attach the resulting `file_id` in a follow-up step.
- Block a transition until the recipient acknowledges — use [Approval Gate](/docs/primitives/approval-gate).
- Exceed the rate limit. Notifier is capped at 100 invocations per tenant per minute across all notifier bindings; excess invocations are skipped and recorded.
