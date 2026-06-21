---
title: Auditor Room
summary: The audit-engagement workflow — invite an external auditor, give them a deterministic sample of evidence, export an OSCAL JSON document, ship them a ZIP package with everything they need.
order: 4
---

# Auditor Room

An **engagement** is a time-boxed audit. You create one when
your auditor is ready to start work. Each engagement
references a framework and a window (a start date and an end
date) — the platform samples only evidence taken inside that
window.

## Engagement lifecycle

| Status | What it means |
| --- | --- |
| `draft` | You're shaping the engagement; not yet shared with the auditor. |
| `in_progress` | The auditor has access and can pull samples. |
| `submitted` | You've handed the engagement off to the auditor; further evidence cannot be added. |
| `closed` | The auditor has signed off; the engagement is archived. |

Each status change is appended to the engagement's history —
the platform does not rewrite a transition once it's been
written. (This is the same append-only event-log discipline the
rest of the platform follows.)

## The Auditor role

When you invite an external auditor, the platform ensures a
built-in **Auditor** system role exists on your tenant and
assigns the invitee to it. The Auditor role is scoped to the
engagement — it carries read access to evidence, the sampler,
and the OSCAL export endpoints, and nothing else. See
[Permissions](/docs/permissions) for the broader role model.

## Deterministic sampling

The sampler is the load-bearing part of the Auditor Room. It
picks a subset of the engagement's population (the set of
evidence rows in window) and presents it to the auditor for
inspection.

**The guarantee:** the same engagement window yields the same sample twice. The sampler is a stable hash-modulo over the
population, seeded by the engagement; re-running it never
shuffles the result. This matters because the auditor and the
operator need to look at the same rows when discussing a
finding — non-deterministic sampling makes that conversation
impossible.

The sampler does **not** publish its seed formula. If you need
to reproduce a sample outside the platform, export the ZIP
package — every sampled row is in it.

## Evidence vault

Evidence rows live in a tenant-scoped vault.

- `GET /evidence` — list evidence visible to the caller.
- `POST /evidence` — upload a new evidence file or note.
- `POST /evidence/:id/attest` — attest to a piece of evidence.

**Attestations are irrevocable.** Once an operator attests
that a piece of evidence is true, the platform appends the
attestation to the audit trail; it cannot be erased. If the
underlying fact changes, attest to the new evidence and let
the trail show the progression.

Sealed-PDF artifacts from the [e-sign](/docs/esign) program
flow into the vault as a special evidence type — see the
e-sign Trust-and-verify page for what the seal proves.

## OSCAL export

`GET /audit/engagements/:id/export.oscal` returns a single
NIST OSCAL JSON document representing the engagement: the
framework, the controls, the sampled evidence references, and
the engagement metadata. Hand this to your auditor or upload
it to their GRC tool of choice.

## ZIP package

`GET /audit/engagements/:id/package.zip` bundles:

- the OSCAL JSON,
- the sampled evidence files,
- a cover sheet with the engagement window, framework, and
  the population/sample sizes.

The ZIP is what most auditors actually want — it's
self-contained, doesn't require platform access to open, and
is the artifact you keep on file once the engagement closes.

## What's not in the Auditor Room today

- **Auditor findings UI** — the auditor leaves their writeup
  outside the platform.
- **Multi-period engagements** — every engagement is a single
  window today.
- **Cross-tenant auditor portal** — auditors who serve
  multiple tenants log in to each tenant separately.

## See also

- [Evidence connectors](/docs/compliance-yoke/connectors) —
  the upstream of evidence rows.
- [Trust Center](/docs/compliance-yoke/trust-center) — the
  public-facing companion surface.
- [E-sign](/docs/esign) — sealed-PDF evidence.
- [API reference](/docs/compliance-yoke/api-reference) for the
  exact request and response shapes.
