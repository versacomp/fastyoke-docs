---
title: Lecture M11 — Webhooks, notifications & messaging
summary: Why outbound webhooks ship with HMAC + delivery-id dedup, what the DLQ replay actually replays, and how notifications and messaging share the event spine.
order: 12
---

# Lecture M11 — Webhooks, notifications & messaging

## The mental model

Events the platform raises (job transitions, form
submissions, billing events) reach external systems three
ways: outbound **webhooks**, in-platform **notifications**,
and conversational **messaging**. All three flow through
the same event spine — the difference is who consumes them.

## Key concepts

- **Outbound webhooks.** HMAC-SHA256 signed; the receiver
  verifies with a one-time-shown `signing_secret`. Every
  delivery carries `X-FastYoke-Delivery` so the receiver
  can dedup.
- **Rotate-secret dual window.** When you rotate, the
  platform validates with **both** secrets during a grace
  window. Roll the receiver before the window closes.
- **DLQ.** Failed deliveries (5xx, timeout) accumulate in
  the dead-letter queue with attempt counters. The
  `replay-dlq` endpoint redrives the queue with the
  original delivery ids — the receiver dedups.
- **SSRF guard.** The dispatcher refuses non-HTTPS,
  RFC-1918, link-local, and IPv6 ULA targets. Refusals
  don't count toward the consecutive-failure auto-pause
  threshold — they're a separate counter.
- **Notifications vs. messaging.** Notifications are
  one-way bell-style; messaging is conversational
  (channels, DMs, record-linked threads). Both reuse the
  notification subsystem for routing.

## Common pitfalls

- **Re-serializing the body before HMAC verification.**
  Different JSON serializers reorder keys or change
  whitespace; the hash won't match. Hash the **raw
  bytes** the platform sent.
- **Ignoring the delivery-id.** Without dedup, a DLQ
  replay double-processes every event that recovers.
- **Using a private IP as the webhook target in a
  pinch.** The SSRF guard refuses it. There's no override.

## Where to go next

- Lab: [M11 — Webhooks](/docs/training/labs/M11-webhooks).
- Reference: [/docs/integrations/webhooks](/docs/integrations/webhooks),
  [/docs/notifications](/docs/notifications),
  [/docs/messaging](/docs/messaging).
