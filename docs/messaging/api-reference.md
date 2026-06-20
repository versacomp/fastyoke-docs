---
title: Messaging API reference
summary: REST endpoints for conversations + messages, the mark-read semantics, and the WebSocket kind:"message" envelope.
order: 2
---

# Messaging API reference

This page covers the seven REST endpoints and the WebSocket
envelope that the messaging dock uses. Same surface is available
to any client with a tenant-scoped JWT.

## Authentication

Every endpoint below requires a tenant-scoped JWT — the same
token the rest of the platform uses. See
[Authentication](/docs/auth) for how to mint and present one.

## Conversations

### Create a DM

```http
POST /api/v1/tenant/messaging/conversations
Content-Type: application/json
Authorization: Bearer <tenant JWT>

{ "participant_user_ids": ["u_…", "u_…"] }
```

The platform computes `dm_key = sha256(sorted participant
user_ids)` and returns the existing conversation if one already
matches that key. This is upsert semantics — the same participant
set always lands in the same conversation.

Response: the conversation record, including `id`, `dm_key`,
participants, and `created_at`.

### List your conversations

```http
GET /api/v1/tenant/messaging/conversations
Authorization: Bearer <tenant JWT>
```

Returns the conversations the caller participates in, plus
per-conversation `unread_count`.

## Messages

### List messages in a conversation

```http
GET /api/v1/tenant/messaging/conversations/:id/messages
Authorization: Bearer <tenant JWT>
```

Returns messages in the conversation. Soft-deleted messages are
returned with a non-null `deleted_at`; their body is omitted from
the response.

### Send a message

```http
POST /api/v1/tenant/messaging/conversations/:id/messages
Content-Type: application/json
Authorization: Bearer <tenant JWT>

{ "body": "hi @[Alice](user:u_alice) — can you take a look?" }
```

The body is parsed for `@[Name](user:…)` tokens; matched
`user_id`s each receive a notification (which lights their bell).

### Edit your message

```http
PATCH /api/v1/tenant/messaging/conversations/:id/messages/:mid
Content-Type: application/json
Authorization: Bearer <tenant JWT>

{ "body": "new body" }
```

Sets `edited_at` to `now()`. Returns 403 if the caller is not the
original sender.

### Delete your message

```http
DELETE /api/v1/tenant/messaging/conversations/:id/messages/:mid
Authorization: Bearer <tenant JWT>
```

Soft-deletes — sets `deleted_at` to `now()` and clears the body.
The message stays in place visually as "deleted." Returns 403 if
the caller is not the original sender.

## Mark as read

```http
POST /api/v1/tenant/messaging/conversations/:id/read
Authorization: Bearer <tenant JWT>
```

Sets `last_read_at` on the caller's participant record to
`now()`. Returns 204 with no body.

Unread count is recomputed lazily as the count of non-own
non-deleted messages in the conversation with
`created_at > last_read_at`.

## Errors

- **400** — request validation failure (missing fields,
  malformed JSON).
- **403** — the caller is not a participant of the conversation
  (or, for edit / delete, not the original sender of the
  message).
- **404** — the conversation does not exist in the caller's
  tenant.

Responses use the standard `AppError` JSON envelope. See
[Error envelope](/docs/developers/errors) for the full status-code
table and worked examples.

## Real-time events

### Where to connect

The platform exposes a single multiplexed WebSocket at
`/api/v1/ws`, authenticated with the same tenant JWT (header or
query param per existing platform convention — see
[Authentication](/docs/auth)).

### Envelope shape

The server emits JSON envelopes tagged by `kind`. Messaging uses
`kind: "message"`:

```json
{
  "kind": "message",
  "event": "created",
  "conversation_id": "conv_…",
  "message": {
    "id": "msg_…",
    "sender_user_id": "u_…",
    "body": "…",
    "created_at": "2026-06-19T12:00:00Z"
  }
}
```

### Server-side filter

The server only sends `kind: "message"` envelopes for
conversations the connected user participates in. Clients do
not need to subscribe per-conversation — opening the WebSocket
is enough.

### What events ship today

`event: "created"` only. Edit and delete events are not
broadcast — clients poll the message list after acting.

### Other `kind`s

The same WebSocket carries other event kinds (notifications,
etc.); each `kind` will be documented in its own docs section.
Filter on `kind` and ignore what you don't recognize.

## What's not in the API today

The following are explicit non-features. Same list as the
overview page, scoped to API impact:

- No channels endpoints (no broadcast-style group spaces).
- No entity-threads endpoints (no record-bound conversations).
- No presence or typing endpoints.
- No attachment endpoints.
- No reactions endpoints.
- No full-text search endpoint.
