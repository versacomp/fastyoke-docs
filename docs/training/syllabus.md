---
title: Syllabus
summary: Sixteen modules across eight weeks. Each module has learning objectives, an assigned reading list cross-linked into the reference docs, a lab title, a project deliverable title, and three quiz topics. The capstone in Week 8 ships an end-to-end Tenant Yoke.
order: 2
---

# Syllabus

Every module follows the same five-section shape:

- **Learning objectives** — 3–5 verbs you'll be able to
  perform after the module.
- **Assigned reading** — cross-links into the reference
  docs.
- **Lab** — hands-on exercise title. (Lab materials ship
  in a follow-on PR.)
- **Project deliverable** — what you push to your
  sandbox tenant by the end of the module.
- **Quiz topics** — three bullets you should be able to
  answer cold.

---

## Week 1 — Foundations

### Module M1 — Platform architecture & multi-tenancy

**Learning objectives**
- Explain the single-binary Rust monolith + SPA admin
  shell architecture.
- Articulate the Fleet-of-Files model: one SQLite
  database per tenant, LiteFS replication.
- State the prime directive: every tenant-scoped query
  scopes by `tenant_id`.
- Identify the three platform-scoped tables that are
  the documented exceptions (audit, platform_admins,
  marketplace_entitlements).

**Assigned reading**
- [/docs/](/docs/)
- [/docs/developers/tenant-scoping](/docs/developers/tenant-scoping)

**Lab** — [Trace a tenant-scoped query through the
backend handler stack and identify each tenant_id
checkpoint.](/docs/training/labs/M1-architecture-trace)

**Project deliverable** — A one-page diagram of your
target tenant's data-flow path, annotated with the
tenant_id checkpoints.

**Quiz topics**
- Why does every tenant get its own SQLite database?
- What happens if a handler forgets to scope by
  `tenant_id`?
- Name two platform-scoped tables and why they're
  exceptions.

### Module M2 — Auth & token layers

**Learning objectives**
- Enumerate the seven auth-token layers the platform
  ships.
- Distinguish session JWTs from `fy_pat_` tokens from
  the one-hour admin test JWT.
- Configure WorkOS AuthKit SSO at the tenant level
  (conceptual).
- Mint, rotate, and revoke a `fy_pat_` PAT.

**Assigned reading**
- [/docs/auth](/docs/auth)
- [/docs/auth/signin-flows](/docs/auth/signin-flows)
- [/docs/auth/api-tokens](/docs/auth/api-tokens)
- [/docs/auth/sso-with-workos](/docs/auth/sso-with-workos)

**Lab** — [Mint a one-hour test token, hit `/auth/me`,
then mint a `fy_pat_` token and compare behavior.](/docs/training/labs/M2-token-walkthrough)

**Project deliverable** — A `curl`-only authentication
walkthrough script that hits three tenant-API
endpoints with three different token types.

**Quiz topics**
- What scope vocabulary are extension and PAT scopes
  drawn from?
- What does the WorkOS SSO callback resolve a user's
  tenant by?
- Why is the one-hour test JWT not subject to the
  `fy_pat_` "hard refusals"?

---

## Week 2 — Core primitives I

### Module M3 — Entities

**Learning objectives**
- Author a JSON-payload schema for a domain entity.
- Apply field-level annotations (labels, options,
  constraints).
- Use the entity REST surface for create / read /
  update / delete.
- Understand the schemaless storage contract and what
  it means for migrations.

**Assigned reading**
- [/docs/entities](/docs/entities)

**Lab** — [Author a `vehicle` entity with annotations
and exercise the full REST surface.](/docs/training/labs/M3-entities)

**Project deliverable** — A two-entity domain (e.g.,
`patient` and `appointment`) with cross-referenced
fields.

**Quiz topics**
- How does the platform validate entity payloads
  without a column schema?
- What's the difference between a label annotation and
  an option annotation?
- What does deleting an entity do to historical FSM
  job rows that referenced it?

### Module M4 — Forms

**Learning objectives**
- Compose a multi-field form with branching logic.
- Validate submissions against a zod-mirrored schema.
- Wire a public-link submission flow.
- Configure anti-abuse defenses on a public form.

**Assigned reading**
- [/docs/forms](/docs/forms)
- [/docs/forms/tutorials/301-anti-abuse](/docs/forms/tutorials/301-anti-abuse)

**Lab** — [Build a public-link patient intake form with
file-upload validation and rate-limit protection.](/docs/training/labs/M4-forms)

**Project deliverable** — A submitted form that feeds
into an FSM job created at submit time.

**Quiz topics**
- Why are form schemas zod-mirrored and not raw JSON
  Schema?
- What's the anti-abuse threshold before a public form
  rate-limits?
- How does a public-link form authenticate the
  submitter?

---

## Week 3 — Core primitives II

### Module M5 — FSM authoring

**Learning objectives**
- Author an FSM schema with states, transitions, and
  JSONLogic guards.
- Explain the sandboxed evaluator and why no host
  syscalls leak through.
- Detect and remove orphan states in a schema.
- Read and write deterministic-replay-safe guards.

**Assigned reading**
- [/docs/workflows](/docs/workflows)

**Lab** — [Author a 5-state lifecycle for a service
ticket with JSONLogic guards on each transition.](/docs/training/labs/M5-fsm-authoring)

**Project deliverable** — An FSM schema with three
guard expressions and a hand-traced sample run.

**Quiz topics**
- Why is JSONLogic the guard language and not a host
  function?
- What's an orphan state, and how does the validator
  catch it?
- What does the guard sandbox refuse?

### Module M6 — Jobs

**Learning objectives**
- Drive an entity through an FSM via job transitions.
- Use the admin cancel override correctly (and know
  when not to).
- Author a self-loop transition for retries or
  counters.
- Read the immutable event log for one job.

**Assigned reading**
- [/docs/jobs](/docs/jobs)

**Lab** — [Override one job to a terminal state using
the admin endpoint, then inspect the resulting event_log
row.](/docs/training/labs/M6-jobs)

**Project deliverable** — A job lifecycle screenshot
including a self-loop transition and a successful
admin override, with the matching `event_log` rows
shown.

**Quiz topics**
- What does the admin override bypass?
- Why is the event log append-only?
- When is a self-loop the right shape?

---

## Week 4 — Authoring surfaces

### Module M7 — Pages & App Builder

**Learning objectives**
- Compose a block-based page from the SDK component
  catalog.
- Set navigation order in an App.
- Customize an App's theme and CSS.
- Use the Advanced App Builder (LLM-assisted).

**Assigned reading**
- [/docs/pages](/docs/pages)
- [/docs/apps](/docs/apps)
- [/docs/apps/advanced](/docs/apps/advanced)

**Lab** — [Build a 3-page App that surfaces a custom
entity list, a form, and a dashboard widget.](/docs/training/labs/M7-pages-and-apps)

**Project deliverable** — Your App rendered in the
admin shell with a theme override.

**Quiz topics**
- What's the difference between a Page and an App?
- What does the Advanced App Builder add over the
  standard one?
- How does theme inheritance work across nested
  pages?

### Module M8 — Yoke Studio

**Learning objectives**
- Use Studio's persistent draft model.
- Capture and restore a named snapshot.
- Walk through the wizard to install a Yoke draft.
- Edit a published Yoke in place via Studio.

**Assigned reading**
- [/docs/apps/studio](/docs/apps/studio)
- [/docs/apps/studio-wizard](/docs/apps/studio-wizard)

**Lab** — [Build a 2-board Yoke in Studio, capture a
named snapshot, edit, restore.](/docs/training/labs/M8-yoke-studio)

**Project deliverable** — A Studio draft restored
from snapshot, deployed to your tenant.

**Quiz topics**
- What does Studio autosave persist between sessions?
- What's the difference between a named snapshot and
  a draft version?
- What's the "edit a published Yoke" in-place flow?

---

## Week 5 — Extending the platform

### Module M9 — Extensions

**Learning objectives**
- Read an extension manifest and identify required
  scopes.
- Use the SDK to call the tenant API from inside an
  extension.
- Sign and install an extension bundle.
- Articulate the four "hard refusals" extensions are
  bound by.

**Assigned reading**
- [/docs/extensions](/docs/extensions)
- [/docs/sdk](/docs/sdk)
- [/docs/recipes/extension-authoring](/docs/recipes/extension-authoring)

**Lab** — [Sign and install the starter extension; verify
it against the platform's signing roster.](/docs/training/labs/M9-extensions)

**Project deliverable** — A signed extension that
fetches an entity count and renders it as a card in
the admin shell.

**Quiz topics**
- What's the TTL of an extension JWT and why?
- What does `require_scope()` enforce?
- Why can't an extension install another extension?

### Module M10 — Marketplace & Tenant Yokes

**Learning objectives**
- Install a public Marketplace listing into a tenant
  and inspect what it seeds.
- Author a Tenant Yoke (custom listing) end-to-end.
- Use the clone-from-source flow to start from an
  existing listing.
- Understand the three pricing models and the
  retention contract.

**Assigned reading**
- [/docs/marketplace](/docs/marketplace)
- [/docs/marketplace/install-and-uninstall](/docs/marketplace/install-and-uninstall)
- [/docs/marketplace/tenant-yokes](/docs/marketplace/tenant-yokes)

**Lab** — [Install Yoke Ledger, inspect the seeded
schemas and accounts, then uninstall and verify the
data is retained.](/docs/training/labs/M10-marketplace)

**Project deliverable** — A Tenant Yoke you authored
(cloned from any seed) installed into a sibling
tenant.

**Quiz topics**
- What does the retention contract guarantee at
  uninstall?
- What's `owned_schemas` and why is uniqueness
  enforced?
- What are the three pricing models?

---

## Week 6 — Events, messaging, data ops

### Module M11 — Webhooks + Notifications + Messaging

**Learning objectives**
- Subscribe to outbound webhooks, verify HMAC, and
  read the delivery log.
- Replay a DLQ batch.
- Understand the SSRF guard and what it refuses.
- Use the in-platform bell and DMs.

**Assigned reading**
- [/docs/integrations/webhooks](/docs/integrations/webhooks)
- [/docs/notifications](/docs/notifications)
- [/docs/messaging](/docs/messaging)

**Lab** — Subscribe to `job.transition`, fire one, and
verify the HMAC end-to-end with three language
samples.

**Project deliverable** — A webhook receiver that
verifies signature, deduplicates by delivery ID, and
handles a DLQ replay correctly.

**Quiz topics**
- Why is the SSRF guard non-counting toward
  auto-pause?
- What's the rotate-secret dual-window for?
- What event sources emit outbound webhooks?

### Module M12 — Bulk operations + Reports & dashboards

**Learning objectives**
- Run a bulk-edit preview, inspect the diff, and
  commit.
- Configure a bulk-delete cascade against a real entity
  set.
- Compose a dashboard widget from the aggregate
  vocabulary.
- Subscribe a recipient to a scheduled digest.

**Assigned reading**
- [/docs/bulk-operations](/docs/bulk-operations)
- [/docs/reports](/docs/reports)

**Lab** — Bulk-edit 50 customer records, preview the
diff, then commit; then bulk-delete and recover from
the cascade.

**Project deliverable** — A dashboard for your domain
with at least one bar and one line widget, and a
weekly digest scheduled to your own email.

**Quiz topics**
- What's the preview-and-commit contract guarantee?
- How are aggregates evaluated — at request time or
  precomputed?
- Why does a digest carry a UTC hour and not a
  timezone?

---

## Week 7 — Specialized Yokes

### Module M13 — Compliance Yoke + Yoker

**Learning objectives**
- Run a framework-readiness sweep against a tenant.
- Connect a single evidence connector (GitHub or AWS).
- Use the Auditor Room to ship an OSCAL export.
- Configure Yoker's RAG corpus for a tenant.

**Assigned reading**
- [/docs/compliance-yoke](/docs/compliance-yoke)
- [/docs/yoker](/docs/yoker)

**Lab** — Connect the GitHub evidence connector and
generate a Trust Center posture page.

**Project deliverable** — An Auditor Room engagement
on a sandbox tenant, with an OSCAL export and ZIP
package downloaded.

**Quiz topics**
- What does deterministic sampling guarantee about
  the Auditor Room?
- What corpus does Yoker index, and what's the gating
  tier?
- What's an evidence connector vs. an attestation?

### Module M14 — Yoke Ledger + E-signatures

**Learning objectives**
- Edit a Chart of Accounts and author a Posting Rule.
- Drive a journal entry through draft → posted →
  reversed.
- Configure a form-with-signing or upload a PDF
  envelope.
- Verify a sealed PDF's Certificate of Completion.

**Assigned reading**
- [/docs/yoke-ledger](/docs/yoke-ledger)
- [/docs/esign](/docs/esign)

**Lab** — Author a Posting Rule that books an invoice
to AR/Revenue, then trigger a sample invoice and
verify the rolled-up journal entry.

**Project deliverable** — A signed invoice PDF, with
the Certificate of Completion verified out-of-band.

**Quiz topics**
- Why are journal entries immutable once posted?
- What does the platform's seal prove without a
  Certificate Authority?
- What's a self-loop in posting-rule terms?

---

## Week 8 — Operations and capstone

### Module M15 — Billing & metering + Reseller billing + Strategic Partner

**Learning objectives**
- Read a tenant's metered-usage roll-up and explain
  every line item.
- Configure the prepaid wallet and the auto-suspend
  threshold.
- Author a reseller rate card and a white-label
  billing profile.
- Explain the Strategic Partner consent model
  end-to-end.

**Assigned reading**
- [/docs/billing](/docs/billing)
- [/docs/reseller-billing](/docs/reseller-billing)
- [/partners/inetko](/partners/inetko)

**Lab** — Top up a sandbox tenant's wallet, simulate
metered usage, and trip the low-balance alert.

**Project deliverable** — A reseller rate card and
white-label invoice, plus a Strategic Partner consent
record granted and revoked.

**Quiz topics**
- What's the $10 low-balance threshold for, vs the $0
  suspend threshold?
- Why is the `__default__` sentinel in rate cards
  important?
- Why is Strategic Partner consent per-request and
  not per-session?

### Module M16 — Capstone

No new reading this module. The capstone shape lives in
[Capstone](/docs/training/capstone). Submit when you
believe every rubric category meets or exceeds. You're
done.

---

## What's not in the syllabus today

- Lecture notes per week
- Lab exercises with starter code
- Graded assignments and rubrics
- Quiz banks
- Cohort delivery materials
- Completion certification

Each is an explicit follow-on PR. The syllabus is the
load-bearing structure those PRs slot into.

## See also

- [Capstone](/docs/training/capstone) — the final
  integration project.
- [Tutorials](/docs/tutorials) — the 101/201/301
  on-ramp.
- [Recipes](/docs/recipes) — single-task snippets.
