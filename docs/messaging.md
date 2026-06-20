---
title: Messaging
summary: In-platform 1:1 and group DMs scoped to a tenant, with @mentions that fire notifications, edit/delete on your own messages, and real-time updates.
order: 1
---

# Messaging

FastYoke ships in-platform messaging as a right-toolbar dock —
no separate page, no email round-trip. Today's surface is direct
messages: 1:1 and small-group DMs scoped to a single tenant, with
@mentions that fire notifications and real-time delivery over the
platform's existing WebSocket.

## What you can do today

- **Start a DM with any other tenant member.** Pick a person from
  the directory; the conversation opens in the dock.
- **Start a group DMs with a small handful of members.** The same
  participant set always lands in the same conversation — there's
  no way to accidentally start a second "Alice + Bob + Carol"
  group.
- **Send messages, edit your own, delete your own.** Edited
  messages stay in place with an `edited_at` marker. Deleted
  messages stay in place visually as "deleted" — the platform
  keeps a soft-delete tombstone so the thread doesn't renumber.
- **Mention someone with `@` + name.** The platform creates a
  [notification](/docs/notifications) and lights the recipient's
  bell. The autocomplete source is your tenant's user directory.
- **See unread per conversation.** Opening a thread marks it
  read; the unread count is non-own messages newer than your
  last-read timestamp.

## Where to find it

A dock icon lives on the right toolbar of the admin shell. The
dock is persistent across pages — chat stays open while you
navigate.

## Who can chat with whom

Messaging is **tenant members only**. Cross-tenant DMs are not supported. The
participant list autocompletes from your tenant's user
directory; you can't add someone who isn't a member of the
current tenant.

There is no per-permission gating on messaging beyond tenant
membership. Any member can DM any other member; any member can
start a group with any subset of members.

## What's not in messaging today

The following are explicit non-features today. Customers who need
them should plan accordingly until the platform ships them.

- **Channels** — broadcast-style group spaces with member lists
  distinct from DM dedupe.
- **Entity-linked threads** — messages bound to a record (Yoke
  job, form submission, entity).
- **Presence indicators** — online / away / busy.
- **Typing indicators**.
- **Read receipts** — who has read which message.
- **Attachments** — files inline in chat.
- **Reactions** — emoji on messages.
- **Search** — across all messages.
- **A full `/messages` page** — today the dock is the only
  surface.
- **Offline email** for missed mentions. The notification bell
  lights live; emails for missed mentions come later.

## See also

- [Permissions](/docs/permissions) for the role model that
  governs which members exist in a tenant.
- [Entities](/docs/entities) for the records that future
  entity-linked threads will attach to when SP3 ships.
- [API reference](/docs/messaging/api-reference) for the seven
  REST endpoints and the WebSocket `kind: "message"` envelope.
