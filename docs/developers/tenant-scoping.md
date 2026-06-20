---
title: Tenant scoping
summary: How the platform enforces multi-tenancy on every request, what your code does (and doesn't) send, and why 404 and 403 mean what they mean.
order: 3
---

# Tenant scoping

Multi-tenancy is FastYoke's prime directive. This page documents
the contract between your client code and the platform.

## What the platform enforces

Every API call carries a tenant-scoped JWT. The `tenant_id` is
derived from the JWT — **never** from the request body or
URL. Every database query the platform issues includes
`WHERE tenant_id = <derived id>` (in the platform code this is
the prime directive — a query without the tenant scope is treated
as a security bug).

The practical consequence: there is no way for a client to "ask
about" a different tenant. The JWT scopes the request to exactly
one tenant; the platform takes care of the rest.

## What your code does (and doesn't) send

You send a tenant-scoped JWT. You **do not** send `tenant_id`
fields in the request body.

**Correct:**

```http
POST /api/v1/tenant/entities/customer
Authorization: Bearer <tenant-scoped JWT>
Content-Type: application/json

{
  "name": "Acme Corp",
  "email": "ops@acme.example"
}
```

**Incorrect:** sending `tenant_id` in the body. The platform
ignores it; if the body's `tenant_id` differs from the JWT's, the
platform uses the JWT's value (never the body's).

```http
POST /api/v1/tenant/entities/customer
Authorization: Bearer <tenant-scoped JWT>
Content-Type: application/json

{
  "tenant_id": "tnt_other",   ← IGNORED. The JWT wins.
  "name": "Acme Corp"
}
```

## Cross-tenant access patterns

Currently none. There is no way for a single JWT to read or write
data across multiple tenants. Operators who manage multiple tenants
mint separate JWTs per tenant and route requests in their own code.

This is intentional. Cross-tenant access would require an
"admin-of-tenants" identity layer that the platform doesn't ship
today.

## Permissions vs. tenancy

Tenancy is the **outer** boundary — which database the request can
see. Permissions are the **inner** boundary — which actions the
caller can take within that database.

| Status | Meaning |
|--------|---------|
| `404` | The resource is not in your tenant. Could be a wrong id, could be cross-tenant. The platform deliberately collapses both — see [Errors](/docs/developers/errors). |
| `403` | The resource is in your tenant, but your JWT's role doesn't carry the permission this action requires. See [Permissions](/docs/permissions). |

A client that gets a 404 should re-check the id; a client that
gets a 403 should re-check the role assignment.
