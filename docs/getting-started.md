---
title: Getting Started
summary: Sign in, provision a tenant, build your first form, wire it to an FSM.
order: 0
---

# Getting Started

This section takes you from an empty account to a public form whose
submissions spawn FSM-backed jobs in your tenant. Three short pages:

::section-cards{section="getting-started"}
::

## Core concepts, in one paragraph

A **tenant** is your isolated workspace — its own SQLite file, its
own user list, its own FSM schemas. Inside the tenant you design
**schemas** (finite-state machines with guards + actions) that run
against **jobs**. Jobs carry **entity records** (JSON payloads in
`entity_records.data_payload`) as their context. **Forms** collect
input from anyone and can `SPAWN_JOB_FROM_FORM` on submit, so a
public URL becomes a typed ingress point for your workflow.

## What you'll have at the end

- A provisioned tenant, bound to the machine you signed up on.
- A two-state FSM (`received → processed`) with one named transition.
- A public form that creates a job in the `received` state.
- An admin view listing the job and letting you fire the transition.

::callout{type="tip" title="Five-minute version"}
If you already know the ropes, skip to
[Your first form](/docs/getting-started/first-form) — it references
back into Installation only where you genuinely need it.
::

Building an integration against the API? The
[ten-minute first-integration tutorial](/docs/tutorials/101-first-integration)
is the fastest way to confirm the contract works
end-to-end.
