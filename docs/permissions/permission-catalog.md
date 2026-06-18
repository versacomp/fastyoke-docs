---
title: Permission catalog
summary: Every permission id FastYoke ships, grouped by domain. The single source of truth lives in backend/src/permissions/catalog.rs.
order: 2
---

# Permission catalog

Every permission FastYoke ships is listed below, grouped by domain.
Custom roles select any subset of these; the four [system roles](/docs/permissions)
each hold a fixed subset defined in code.

::callout{type="info"}
The canonical list lives in `backend/src/permissions/catalog.rs`.
Hit `GET /tenant/permissions` from any tool that needs the live set —
the endpoint returns the same data as JSON.
::

## Users & roles

| Id | Label | Description |
|----|-------|-------------|
| `users.read` | View users | List and view tenant users. |
| `users.invite` | Invite users | Send invitations to new users. |
| `users.update` | Update users | Edit user profile and role assignment. |
| `users.delete` | Remove users | Remove users from the tenant. |
| `roles.read` | View roles | List and view roles and their permissions. |
| `roles.manage` | Manage roles | Create, edit, and delete custom roles. |

## FSM schemas

| Id | Label | Description |
|----|-------|-------------|
| `fsm.read` | View FSMs | List and view FSM schemas. |
| `fsm.create` | Create FSMs | Author new FSM schemas. |
| `fsm.update` | Edit FSMs | Edit FSM drafts (incl. annotations). |
| `fsm.delete` | Delete FSMs | Remove FSM schemas. |
| `fsm.publish` | Publish FSMs | Promote a draft FSM to live. |

## Jobs

| Id | Label | Description |
|----|-------|-------------|
| `jobs.read` | View jobs | List and view job records. |
| `jobs.create` | Create jobs | Spawn new jobs against an FSM. |
| `jobs.transition` | Transition jobs | Drive a job through state transitions. |
| `jobs.cancel` | Override / cancel | Force-cancel a job (admin override; bypasses guards). |

## Entities

| Id | Label | Description |
|----|-------|-------------|
| `entities.read` | View entities | List and view entity records. |
| `entities.create` | Create entities | Insert new entity records. |
| `entities.update` | Update entities | Edit existing entity records. |
| `entities.delete` | Delete entities | Remove entity records. |
| `entities.export` | Export entities | Bulk export entity data. |

## Forms

| Id | Label | Description |
|----|-------|-------------|
| `forms.read` | View forms | List and view form definitions. |
| `forms.create` | Create forms | Author new forms. |
| `forms.update` | Edit forms | Edit form drafts. |
| `forms.publish` | Publish forms | Promote a draft form to live. |
| `forms.delete` | Delete forms | Remove form definitions. |
| `forms.submissions.read` | View submissions | View data submitted through forms. |
| `forms.submissions.export` | Export submissions | Bulk export form submission data. |

## Files

| Id | Label | Description |
|----|-------|-------------|
| `files.read` | View files | View attachments and files. |
| `files.upload` | Upload files | Attach new files to entities and forms. |
| `files.delete` | Delete files | Remove attachments. |

## Apps

| Id | Label | Description |
|----|-------|-------------|
| `apps.read` | View apps | List installed apps and extensions. |
| `apps.install` | Install apps | Install new app extensions. |
| `apps.uninstall` | Uninstall apps | Remove app extensions. |

## PDF templates

| Id | Label | Description |
|----|-------|-------------|
| `pdf.read` | View PDF templates | View overlay and auto-typeset PDF templates. |
| `pdf.update` | Edit PDF templates | Edit overlay region maps and PDF settings. |
| `pdf.publish` | Publish PDF templates | Promote PDF template drafts to live. |

## Tenant config

| Id | Label | Description |
|----|-------|-------------|
| `tenant.settings.view` | View settings | Open the tenant Settings area (branding, themes, connections, plan, etc.). |
| `tenant.branding.update` | Edit branding | Edit tenant branding (logo, colors, names). |
| `tenant.theme.update` | Edit themes | Edit shared themes. |
| `tenant.billing.read` | View billing | View invoices and current commercial tier. |
| `tenant.billing.manage` | Manage billing | Open Checkout, change tier, manage Stripe portal. |
| `tenant.sso.manage` | Manage SSO | Configure WorkOS / SAML / OIDC connections. |
| `tenant.workspace.update` | Rename workspace | Change the tenant's display name. |
| `tenant.connections.manage` | Manage connections | Configure LLM and integration credentials. |
| `tenant.auth.manage` | Manage auth proof | Rotate JWKS keys and configure auth-proof signing. |

## Audit & logs

| Id | Label | Description |
|----|-------|-------------|
| `audit.read` | View audit log | View role-change and admin-override logs. |

## API tokens

| Id | Label | Description |
|----|-------|-------------|
| `tokens.manage` | Manage API tokens | Mint, list, revoke per-tenant fy_pat_* tokens. |

## Pages

| Id | Label | Description |
|----|-------|-------------|
| `pages.manage` | Manage pages | Author and publish standalone custom pages. |

## Projects

| Id | Label | Description |
|----|-------|-------------|
| `projects.manage` | Manage projects | Create and organize project containers for forms / pages / extensions. |

## Wasm

| Id | Label | Description |
|----|-------|-------------|
| `wasm.execute` | Execute Wasm | Invoke admin-only Wasm execution endpoints. |

## Encryption

| Id | Label | Description |
|----|-------|-------------|
| `tenant.encryption.manage` | Manage encryption add-on | Activate, rotate, backfill, and offboard the PII / SPI encryption add-on. |
| `tenant.encryption.readstatus` | View encryption status | View encryption health: active version, row counts, last rotation. |

## Identity

| Id | Label | Description |
|----|-------|-------------|
| `identity.sources.read` | View identity sources | List identity-bearing entity types declared by tenant admins. |
| `identity.sources.write` | Manage identity sources | Create, edit, and disable identity-bearing entity types. |
| `identity.read` | View identity | View the tenant's WorkOS Organization summary. |
| `identity.update` | Manage identity | Edit WorkOS Organization name + MFA policy, launch Admin Portal for SSO / SCIM setup. |

## Inventory

| Id | Label | Description |
|----|-------|-------------|
| `inventory.read` | View inventory | List and view inventory items, stock levels, and movements. |
| `inventory.write` | Manage inventory | Post stock movements and advance inventory workflows. |

## Auto Dealer

| Id | Label | Description |
|----|-------|-------------|
| `dealer.read` | View dealership | View repair orders, counter tickets, special orders, and payments. |
| `dealer.write` | Manage dealership | Create and advance repair orders, counter tickets, and special orders. |
| `dealer.payments` | Take payments | Charge, void, and refund card-present payments at the counter and cashier. |

## Auto Tech

| Id | Label | Description |
|----|-------|-------------|
| `tech.payments` | Take payments | Charge, void, and refund card-present payments against a repair order. |

## Compliance

| Id | Label | Description |
|----|-------|-------------|
| `compliance.read` | View compliance | View frameworks, controls, findings, and corrective actions. |
| `compliance.write` | Manage compliance | Create and advance findings, controls, and corrective actions. |
| `compliance.verify` | Verify corrective actions | Sign off that a corrective action's evidence is sufficient (privileged). |
| `compliance.audit` | Audit access | Read-only, engagement-scoped access to controls, evidence, and the immutable result history for an external auditor. |

## Yoke

| Id | Label | Description |
|----|-------|-------------|
| `yoke.read` | View Yoke briefs | View the Yoke inbox and brief details. |
| `yoke.decide` | Decide on Yoke briefs | Apply, snooze, or dismiss Yoke briefs. Apply writes a schema_commit; dismiss-with-reason feeds template authoring. |
