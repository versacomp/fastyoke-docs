<!--
  Curation note: add a new ::security-fix block here in the same
  CHANGELOG-curation session where you bless the corresponding
  ### Security bullet. Keep prose plain-English. NEVER link to
  the private fastyoke2 GitHub repo. External authorities (RUSTSEC
  / GHSA / CVE / vendor advisories) only.
-->
---
title: Security Fixes — FastYoke
summary: Known security findings and their fix status across FastYoke releases.
order: 6
---

::hero{eyebrow="Trust" title="Security fixes" tagline="A running log of known security findings and the releases that resolved them."}
::

<section class="mx-auto max-w-3xl px-4 py-8 text-base leading-relaxed text-[var(--brand-text-secondary)]">

FastYoke publishes each material security fix once it ships. This page lists
known findings — both ones we found internally and ones reported by customers
or external researchers — and shows the release that resolved each. For our
broader privacy posture and legal terms, see [Privacy](/privacy) and
[Terms](/terms).

::callout{type="info" title="Reporting a vulnerability"}
Email **security@fastyoke.io** with steps to reproduce and any impact you've
observed. We acknowledge new reports within two business days.
::

Status: **Fixed** — resolved in the listed release; customers on that version
or newer are not exposed. **Mitigated** — a compensating control prevents
exploitation; a code-level fix is pending. **Investigating** — acknowledged
and under triage.

::security-fix
---
title: TLS hostname-verification regression in lettre's boring-tls backend
status: fixed
fixedIn: "3.1.2"
fixedAt: "2026-05-15"
advisory:
  label: RUSTSEC-2026-0141
  url: https://rustsec.org/advisories/RUSTSEC-2026-0141
---

A bug in lettre 0.11.21's boring-tls integration silently disabled TLS
hostname verification for callers on that backend. FastYoke compiles lettre
with rustls, so no shipped release of FastYoke was exposed to the vulnerable
code path. We bumped to 0.11.22 to clear the advisory and remove the flagged
crate from our dependency tree.
::

::security-fix
---
title: Production CORS now refuses to start without an allow-list
status: fixed
fixedIn: "3.1.0"
fixedAt: "2026-05-15"
---

The backend previously fell back to a permissive
`Access-Control-Allow-Origin: *` when `CORS_ALLOWED_ORIGINS` was unset. We
changed production deploys to refuse to start without an explicit allow-list,
matching the fail-closed posture used for the JWT signing secret. Sandbox and
local-dev environments keep the permissive fallback so browser-based testing
isn't blocked.
::

::security-fix
---
title: API-layer audit remediation
status: fixed
fixedIn: "3.1.0"
fixedAt: "2026-05-15"
---

We ran a comprehensive audit of the API layer covering tenant-isolation gaps,
SSRF egress vectors, authentication hardening, and reflected XSS surfaces.
All findings were remediated and shipped before any release of v3 reached
general availability.
::

::security-fix
---
title: Connection-listing endpoints now require explicit permission
status: fixed
fixedIn: "3.1.0"
fixedAt: "2026-05-15"
---

The endpoints that list and read integration connections previously allowed
any authenticated identity to enumerate which providers a tenant had
configured — a useful reconnaissance signal for an attacker holding a
low-privilege token. They now require the same permission that gates
connection creation and deletion. The cancel endpoint for FSM jobs was also
tightened to resolve the tenant id from the session, not from request input.
::

::security-fix
---
title: Per-route rate limiting on unauthenticated surfaces
status: fixed
fixedIn: "3.1.0"
fixedAt: "2026-05-15"
---

Three unauthenticated request paths — login, invitation acceptance, and
public form submission — previously had no per-IP throttle and were bounded
only by CPU. Each now mounts a token-bucket limiter with a `Retry-After`
response on rejection. Login and invitation acceptance throttle per source
IP; form submission throttles per `(source IP, form slug)` so cross-form
abuse from one IP is caught while a single user submitting to multiple forms
is not penalised.
::

<p class="mt-12 text-xs" style="color: var(--brand-text-secondary)">Last updated: 2026-05-29</p>

</section>
