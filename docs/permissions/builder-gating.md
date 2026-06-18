---
title: Builder permission gating
summary: How the frontend hides authoring surfaces from users who lack the underlying permission.
order: 4
---

# Builder permission gating

The FastYoke backend has always enforced permission checks on every
authoring API. A user without `forms.update` trying to publish a form
draft gets a 403 — that has been true since the catalog shipped.

What was missing was the **frontend** companion: until this change,
the builder UI for Forms, Pages, Apps, Workflows, and Entities
rendered the same way for every user, regardless of whether the user
could actually submit changes from it. A viewer landing on the Forms
builder saw the full editor and only discovered they had no permission
when the publish button returned a 403.

This page documents the `RequirePermission` route guard that fixed
that gap, and shows how to use the same primitive in your own
extension.

## What the guard does

`RequirePermission` is a route-level guard component. Two effects:

1. **Navigation entries pointing at a gated route are hidden** from
   users who lack the permission. The sidebar / app launcher / dropdown
   item simply does not render.
2. **Direct URL access to the gated route redirects** to the user's
   nearest accessible page (their home dashboard by default). A
   deep-linked URL no longer renders a 403 error page.

Both effects ship together. A hidden surface that is reachable by URL
is a leak; the URL redirect closes it.

## Which builders are now gated

Each builder requires the corresponding `<subsystem>.author` permission:

| Builder | Permission |
|---------|------------|
| Forms builder | `forms.create`, `forms.update` |
| Pages builder | `pages.manage` |
| Apps builder | `apps.install` |
| Workflows builder | `fsm.create`, `fsm.update`, `fsm.publish` |
| Entities builder | `entities.create`, `entities.update` |

A user must hold **at least one** of the permissions listed for a
builder to reach it. Once they reach it, individual operations inside
the builder check the more specific permission (e.g. you can enter
the Forms builder with `forms.create` but the Publish button stays
disabled without `forms.publish`).

## Using the guard in your extension

If your extension ships a custom builder or admin surface, wrap its
route in `RequirePermission` and name the permission your surface
requires:

```ts
import { RequirePermission } from '@fastyoke/sdk';

export default {
  routes: [
    {
      path: '/extensions/my-builder',
      component: () => import('./MyBuilder.vue'),
      meta: {
        guard: RequirePermission({ permission: 'myext.author' }),
      },
    },
  ],
};
```

Declare the permission id (`myext.author`) in your extension manifest
so it appears in the role editor — admins can then grant it to whatever
custom role makes sense for their tenant.

## What the guard does NOT do

`RequirePermission` is a **UX layer**, not a security boundary. The
security boundary is the backend permission check on the API the
surface calls. An extension that gates only at the frontend and
forgets the backend check is broken — a determined user can call the
API directly with a tenant JWT and bypass the guard entirely.

Rule: every gated frontend surface must call a backend endpoint that
performs the same permission check server-side. The guard is for
hiding the surface; the API check is for enforcing access.

## See also

- [Permissions overview](/docs/permissions) — system roles and custom roles.
- The full [permission catalog](/docs/permissions/permission-catalog).
- [Role-change audit log](/docs/permissions/audit-log) — what gets logged when an admin grants the new permission.
