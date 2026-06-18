---
title: FastYoke Docs
summary: The aPaaS for logistics. Multi-tenant FSMs, schema-driven entities, extensible admin surfaces.
---

# FastYoke Documentation

FastYoke is a single-binary, multi-tenant application platform built
around four ideas:

- **Finite-state machines** drive every job through operator-authored
  transitions. Guards are sandboxed JSONLogic; actions are declarative.
- **Schemaless entities** hold your domain data as JSON payloads,
  with optional field-level [annotations](/docs/entities) for labels,
  options, and constraints.
- **Forms** collect data from anyone (authenticated or anonymous via
  public invites), validate against zod-mirrored schemas, and feed
  submissions back into your FSM.
- **Extensions** let you ship your own React UI bundled against the
  platform SDK and mounted inside the admin shell.

::section-cards
::

## Where to start

::callout{type="tip" title="Tenant admin?"}
Start with [Getting Started](/docs/getting-started) — it walks you
  from sign-in to a live public form in under ten minutes.
::

::callout{type="tip" title="Integrator?"}
Head to the [SDK quickstart](/docs/sdk/quickstart) or the
  [CLI reference](/docs/cli) to wire FastYoke into your own tooling.
::

::callout{type="tip" title="Setting up access?"}
See [Permissions](/docs/permissions) for the role model, the full
  permission catalog, the role-change audit log, and the frontend
  builder-gating primitive.
::

## Versioning

Docs follow the deployed platform — there is no multi-version sidebar.
Surfaces that are on the way out carry a visible deprecation callout
with a pointer to the replacement; see the
[Deprecation policy](/docs/deprecation-policy) for the contract.
