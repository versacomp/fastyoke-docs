---
title: Installation
summary: Sign in, confirm your tenant assignment, invite teammates.
order: 1
---

# Installation

FastYoke is a single-binary application platform. There's no per-tenant
install step on your side — your platform admin (or
self-service signup) provisions a tenant for you, and everything from
there happens inside the admin shell at **`/admin`**.

## Prerequisites

- A verified email address.
- An invite link (from your platform admin) OR a self-service signup
  flow if your deployment has one enabled.

## The sign-in flow

1. Click the invite link. You land on `/accept-invite/:token`.
2. Set a password — minimum 8 characters. Passwords are stored as
   Argon2id hashes on the server; FastYoke never sees the plaintext
   after the initial set.
3. You're signed in and redirected to `/admin`.

::callout{type="info" title="Roles at a glance"}
Every tenant user has a role: `admin` or `operator`. Admins configure schemas, forms, and
extensions; operators run jobs against them. A handful of surfaces
(FSM edits, form publishing, extension install) are admin-only and
will return `403` for operators.
::

## Confirming your tenant assignment

Visit `/admin/settings/branding`. The **Tenant** block at the top of
the page shows four read-only fields:

- **Name** — the tenant's display name.
- **Tenant id** — the UUID used in every API request.
- **Assigned machine** — the Fly.io VM your tenant's data lives on.
- **Current machine** — the VM serving the request you just made.

In a single-VM deployment the last two are the same (or both blank in
dev). In a multi-VM deployment they can differ for one round-trip:
the platform's `fly-replay` middleware detects the mismatch and
bounces the request to the assigned machine transparently, so this
is purely informational unless you're debugging routing.

## Inviting teammates

From `/admin/settings`:

1. Open **Tenant Admins** (or **Users** for the operator role).
2. Enter the teammate's email and role.
3. Click **Send invite**. The backend mints a one-time
   `accept-invite` token with a 7-day expiry.

Users on WorkOS-provisioned tenants skip the invite flow entirely —
they sign in via SSO and get JIT-provisioned on first callback.

## Troubleshooting

::callout{type="warn" title="401 on every API call"}
Your browser is holding a stale token. Click the avatar → Log out,
then sign back in. The `/auth/me` endpoint re-hydrates the session
on every mount, so you never need to hard-refresh.
::

::callout{type="warn" title="Invite link says 'expired or already accepted'"}
Invite tokens are one-shot. Ask your platform admin for a fresh
link — the old token isn't reusable even if you never opened it.
::

## Next steps

Head to [Your first form](/docs/getting-started/first-form) to build
something real against your new tenant.
