---
title: Notifications
summary: A per-user inbox of events that need your attention. Today the platform fires notifications on @mentions only; the bell is real-time and per-user.
order: 1
---

# Notifications

Notifications are a per-user inbox of events that need your
attention. A bell icon in the admin shell lights when something
happens that's directed at you, and a badge counts your unread
items. Today the platform fires notifications on `@mentions` only
— entity-note mentions and messaging mentions. Other event kinds
(workflow transitions, e-signature events, form submissions, etc.)
are explicit non-features today; see [What's not a notification
kind today](#whats-not-a-notification-kind-today) below.

## The bell

A single bell icon in the top bar of the admin shell. Real-time —
incoming notifications light the bell live over the platform's
WebSocket; no refresh needed. The badge counts your unread items.
Clicking the bell opens your feed; clicking an entry marks it
read and, where there's a deep link to a host record, takes you
to the subject.

The bell is per-user — your unread count doesn't reflect anyone
else's. There's no shared inbox today, no tenant-wide "all
notifications" view.

## Notification kinds today

Two kinds ship today. The table names each kind, when it fires,
the `subject_kind` it points at, and what's in the `snippet`.

| Kind | Fires when… | `subject_kind` | What's in `snippet` |
|---|---|---|---|
| `note_mention` | Someone `@mentions` you in an entity note | the host entity kind | a few words of the note body |
| `message_mention` | Someone `@mentions` you in a messaging conversation | `"conversation"` | a few words of the message body |

Every notification carries these fields when you fetch it:

- `id` — the notification's own id.
- `kind` — one of the values above.
- `actor_email` — who triggered it (the person who mentioned you).
- `subject_kind` — what kind of record the notification points at.
- `subject_id` — the id of that record.
- `note_id` — populated for `note_mention` and null for everything
  else.
- `snippet` — a short preview of what was said.
- `read_at` — null until you mark it read, then a timestamp.
- `created_at` — when the notification fired.

## What's not a notification kind today

The following are explicit non-features today. Customers who need
them should plan accordingly until the platform ships them.

- **FSM workflow events** — transitions, guard failures, terminal-
  state arrivals.
- **E-signature events** — signer reminders, completion notices,
  declines.
- **Form-submission notifications** — a new submission landing.
- **Marketplace app events** — installs, updates, errors from
  installed apps.
- **System-admin alerts** — tenant cap, quota warnings, billing
  events.
- **Email fallback** for missed in-app notifications. The bell
  lights live; emails for missed-while-offline notifications come
  later.

## Tenant scope

Notifications are tenant-scoped per the platform's prime
directive. You receive notifications only for events inside the
tenant the firing actor was in — no cross-tenant fan-out. See
[Tenant scoping](/docs/developers/tenant-scoping) for the full
contract.

## See also

- [API reference](/docs/notifications/api-reference) — REST
  endpoints + the WebSocket `kind: "notification"` envelope.
- [Messaging](/docs/messaging) — the source of `message_mention`
  notifications.
- [Entities](/docs/entities) — entity notes (the source of
  `note_mention` notifications).
