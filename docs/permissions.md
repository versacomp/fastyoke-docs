---
title: Permissions
summary: Roles, the permission catalog, the audit log, and the frontend builder-gating primitive.
order: 1
---

# Permissions

FastYoke ships role-based access control with a fixed catalog of
permissions and four built-in system roles. Tenant admins can also
define their own custom roles by selecting any subset of the catalog.
Every API call and every authoring surface checks the calling user's
effective permissions on the way in.

## Tenant scope

Roles and role assignments are **tenant-scoped**. A user invited to two
tenants gets a separate role assignment in each. There is no cross-
tenant role sharing and no cross-tenant permission inheritance.

## The four system roles

| Role | What it can do |
|------|----------------|
| `system:admin` | Everything — the only role that can create, edit, and delete custom roles, manage users, manage billing, and configure SSO. |
| `system:manager` | Owns the full content surface: FSM schemas, jobs, entities, forms, files, apps, PDFs, pages, projects, branding, themes, and connections. Cannot manage users or roles, cannot manage billing, cannot manage SSO. |
| `system:member` | Day-to-day operations: read everywhere, plus create / update entities, transition jobs, edit form drafts, upload files. Designed for operators who run the workflow without authoring it. |
| `system:viewer` | Read-only on every surface. Sees the audit log too (read-only is read-only — there is no surface a viewer can change). |

## Custom roles

An admin opens **Users → Roles**, creates a new role, names it, checks
the permissions it should hold, and saves. Custom roles inherit nothing
implicitly — every permission is an explicit checkbox. A user can be
assigned a system role *and* a custom role; their effective permissions
are the union of both.

## Effective permissions

A user's effective set is the **union** of every role assigned to
them. Permissions are additive — there is no deny-list, no negative
grant. A user with `system:viewer` plus a custom role that adds
`forms.update` can edit form drafts.

## See also

- The full [permission catalog](/docs/permissions/permission-catalog) — every id, grouped by domain.
- The [role-change audit log](/docs/permissions/audit-log) — what gets recorded when an admin changes a role assignment.
- The [builder-gating primitive](/docs/permissions/builder-gating) — what `RequirePermission` does in the frontend.
