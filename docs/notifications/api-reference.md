---
title: Notifications API reference
summary: REST endpoints for listing notifications, fetching the unread count, and marking as read — plus the WebSocket kind:"notification" envelope.
order: 2
---

# Notifications API reference

This page covers the three REST endpoints and the WebSocket
envelope that the bell uses. The same surface is available to any
client with a tenant-scoped JWT.

## Authentication

Every endpoint below requires a tenant-scoped JWT — the same token
the rest of the platform uses. See [Authentication](/docs/auth)
for how to mint and present one.

## List notifications

```http
GET /api/v1/tenant/notifications
Authorization: Bearer <tenant JWT>
```

Query parameters:

- `unread` (optional, default `false`) — when `true`, returns only
  notifications you haven't marked read yet.
- `limit` (optional, default `50`) — clamped to `1..200`. The
  newest notifications come first.

Response: an array of notification records:

```json
[
  {
    "id": "ntf_…",
    "kind": "note_mention",
    "actor_email": "alice@example.com",
    "subject_kind": "entity",
    "subject_id": "ent_…",
    "note_id": "note_…",
    "snippet": "…can you take a look?",
    "read_at": null,
    "created_at": "2026-06-20T12:00:00Z"
  }
]
```

Ordered newest first (`created_at DESC`).

## Unread count

```http
GET /api/v1/tenant/notifications/unread-count
Authorization: Bearer <tenant JWT>
```

Response:

```json
{ "unread": 7 }
```

The source of the bell badge. Cheap (a single count query); safe
to poll on a short interval if you don't want to wire the
WebSocket.

## Mark as read

```http
POST /api/v1/tenant/notifications/mark-read
Content-Type: application/json
Authorization: Bearer <tenant JWT>
```

Body is either a specific list of ids OR a sweep:

```json
{ "ids": ["ntf_abc", "ntf_def"] }
```

```json
{ "all": true }
```

The platform rejects a no-op request — you must supply at least
one id in `"ids"` or set `"all": true`. Returns `400 BadRequest`
otherwise.

On success, returns 200 with `{ "marked": <number> }` — the count
of rows transitioned from unread to read.

## Errors

Standard `AppError` envelope used elsewhere on the platform. The
relevant statuses for notifications:

- `400` — no-op `mark-read` (neither `ids` nor `all` supplied).
- `401` — missing or invalid JWT.
- `404` — a specific id in `mark-read` doesn't belong to your
  tenant. (The server silently skips ids that aren't yours rather
  than rejecting the whole batch; the success response's `marked`
  count tells you how many actually flipped.)

See [Error envelope](/docs/developers/errors) for the full
status-code table and worked examples.

## Real-time events

### Where to connect

The platform exposes a single multiplexed WebSocket at
`/api/v1/ws`, authenticated with the same tenant JWT. The same
endpoint serves messaging, notifications, and any other event
kind the platform broadcasts.

### Envelope shape

The server emits JSON envelopes tagged by `kind`. Notifications
use `kind: "notification"`:

```json
{
  "kind": "notification",
  "event": "created",
  "notification": {
    "id": "ntf_…",
    "kind": "note_mention",
    "actor_email": "alice@example.com",
    "subject_kind": "entity",
    "subject_id": "ent_…",
    "note_id": "note_…",
    "snippet": "…can you take a look?",
    "created_at": "2026-06-20T12:00:00Z",
    "read_at": null
  }
}
```

The inner `notification` object matches the REST shape exactly,
so client code can share a parser.

### Server-side filter

The server only sends `kind: "notification"` envelopes scoped to
the connected user (`recipient_user_id`). No per-kind
subscription is necessary — you get every kind your user
receives.

### What events ship today

`event: "created"` only. Mark-read doesn't broadcast — clients
refresh the list or unread count after acting locally.

### Other `kind`s

The same WebSocket carries other event kinds (messages,
maybe more in the future). Filter on `kind` and ignore what you
don't recognize.
