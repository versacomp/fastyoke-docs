---
title: Role-change audit log
summary: What gets recorded when an admin changes a role, who can read it, and how to read it programmatically.
order: 3
---

# Role-change audit log

Every change to a role, every role assignment, and every permission
toggle on a custom role is recorded in an **append-only** audit log.
The log is the canonical record of who had what permission at any
point in time.

## What gets recorded

- Assigning a role to a user.
- Revoking a role from a user.
- Creating a custom role.
- Editing a custom role (name, description, permission set).
- Deleting a custom role.
- Toggling any individual permission on a custom role.

Each entry carries the actor email, the target user (when the entry
records a role assignment), the role id, the permission id (when the
entry records a permission toggle), the before / after state, and a
timestamp.

## Immutability

The audit log table is **append-only at the database layer**. There
is no edit endpoint and no delete endpoint. The same invariant holds
for the admin-override log (`__admin_cancel__` events on the FSM
event ledger). A compliance reader can trust that an entry, once
recorded, is permanent.

## Who can read it

The `audit.read` permission. **Every system role holds it by default**,
including `system:viewer`. Read-only is read-only — there is no
surface a viewer can change by reading the log, so the read access is
granted unconditionally.

A custom role with no permissions does NOT hold `audit.read` — it has
to be checked explicitly when the role is created.

## Where to view it in the admin UI

**Users → Audit log** in the admin shell. Entries are paginated newest-
first; each entry expands to show the before / after diff inline.

## Programmatic access

`GET /tenant/audit/role-changes`

- Authentication: the standard tenant JWT.
- Required permission: `audit.read`.
- Pagination: keyset. The response carries a `next_cursor` field;
  pass it as the `cursor` query parameter to fetch the next page.
- Ordering: newest first.

The response is JSON. Each entry includes the actor, the target
(user id or role id), the action, the timestamp, and a `before` /
`after` snapshot.

## See also

- [Permissions overview](/docs/permissions) — the four system roles and how custom roles work.
- The full [permission catalog](/docs/permissions/permission-catalog).
