---
title: SSO with WorkOS
summary: When AuthKit is enabled on your account, sign-in goes through WorkOS. The tenant ↔ WorkOS Organization mirror, the sign-in path, invite acceptance, and the failure modes a tenant admin can hit.
order: 4
---

# SSO with WorkOS

When AuthKit is enabled for your account, sign-in goes
through a WorkOS-hosted page. This page is for the
**tenant admin** — when AuthKit is on, what your team
sees and how the FastYoke side mirrors the WorkOS side.

If AuthKit is not enabled, your team uses the local +
OTP paths in [Sign-in flows](/docs/auth/signin-flows)
instead. Enabling AuthKit is an operator action — see
the operator runbook at the bottom of this page.

## The tenant ↔ WorkOS Organization mirror

Every FastYoke tenant with AuthKit enabled maps **1:1**
to a **WorkOS Organization**. The platform sets up the
mirror on first sign-in; tenant admins don't manage the
WorkOS side directly.

A consequence: changes that affect membership
(invites, removals) take effect on both sides. The
platform writes through to WorkOS on accept/remove so
the two never drift.

## The sign-in path

1. A user opens the FastYoke sign-in page.
2. The page calls `GET /auth/authkit/signin`, which
   redirects to a WorkOS-hosted page.
3. WorkOS authenticates the user against whatever
   identity provider is wired to the Organization
   (Okta, Google, Microsoft, etc.).
4. WorkOS redirects back to
   `GET /auth/authkit/callback` with a code.
5. The platform exchanges the code, mints a tenant JWT
   with the user's `tenant_id` and `role`, and lands
   the user on the admin shell.

The local-password path is bypassed entirely when
AuthKit is on. Users who only had a password before
should expect their old credentials to stop working at
cutover.

## Inviting a user when SSO is on

Invites work the same way they always have, with one
addition: when the invitee accepts, they're added to
both the FastYoke tenant **and** the WorkOS
Organization. Removing a member from the FastYoke
tenant removes them from the WorkOS Organization too.

The platform never asks you to add a user on the WorkOS
side separately. Your only management surface is the
FastYoke tenant — the mirror keeps WorkOS in sync.

## Failure modes a tenant admin can hit

- **"no active tenant for your WorkOS organization."**
  The WorkOS Organization exists but its FastYoke
  tenant binding is broken. The most common cause is a
  tenant that was archived after enablement. Contact
  platform support; the fix is operator-side, not
  self-serve.
- **First sign-in from a brand-new domain returns an
  error.** Enablement isn't complete — the operator
  may have flipped the feature flag without finishing
  the WorkOS Organization setup. See the operator
  runbook.
- **An invited user accepts but doesn't see the
  tenant.** The WorkOS mirror lagged. Sign out, sign
  back in; the mirror finalizes on the next
  authenticated request.

## Operator enablement

Enabling AuthKit for an account is an **operator
action**, not a self-serve toggle. The full enablement
sequence — feature flag, sandbox bake, prod flip,
rollback — is documented in the internal operator
runbook at
`docs/operations/workos-rollout-runbook.md`. The runbook
is the authoritative sequence; this customer-facing page
deliberately doesn't republish it so the runbook and the
docs can't drift out of sync.

## REST surface (read-only for tenant admins)

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/auth/authkit/signin` | Sign-in entry. Sets the WorkOS state envelope and redirects to WorkOS. |
| GET | `/auth/authkit/callback` | WorkOS redirect target. Mints a tenant JWT and lands the user on the admin shell. |

Both endpoints are driven by the browser sign-in flow;
tenant admins don't call them directly.

## What's not in WorkOS SSO today

- **SCIM provisioning** — push user/group updates from
  the identity provider directly into the WorkOS
  Organization.
- **Per-tenant identity-provider configuration** from
  inside the FastYoke admin shell (today the provider
  is wired in WorkOS).
- **AuthKit disablement self-serve** — turning AuthKit
  off is also an operator action.

## See also

- [Sign-in flows](/docs/auth/signin-flows) — the
  local + OTP paths when AuthKit is off.
- [Authentication & Scopes](/docs/auth) — the JWT
  shape AuthKit produces.
- [Permissions](/docs/permissions) — the role assigned
  at callback time.
