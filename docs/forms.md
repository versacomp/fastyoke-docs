---
title: Forms
summary: Build public or authenticated forms that route submissions into entities, jobs, or both.
order: 2
---

# Forms

Forms are FastYoke's typed ingress layer. They collect data from
anyone (or anyone you've authenticated), validate against a schema
the builder owns, and feed submissions into your workflow via
declarative actions.

## What's covered

- [Tutorials](/docs/forms/tutorials) — guided 101 → 201 → 301 path that builds one Patient Intake form across every feature.
- [Recipes](/docs/forms/recipes) — quick, isolated single-task pages.
- [Tier entitlements](/docs/forms/tiers) — what each tier unlocks for the Forms surface.
- [Anti-abuse](/docs/forms/anti-abuse) — Turnstile, honeypot, daily caps, rate limit.
- [Embed forms](/docs/forms/embed) — drop a form into any page via the SDK.
- [HTTP ingestion API](/docs/forms/http-ingestion) — server-to-server submission.
- [Invites (Mint)](/docs/forms/invites) — named, revocable invite links.
- [Vanity PDF filenames](/docs/forms/pdf-vanity-filenames) — readable filenames for submission PDFs.

::section-cards{section="forms"}
::

## Why forms are a first-class surface

Most platforms bolt forms on top of their entity model. FastYoke
treats forms as a separate quarantine zone: submissions land in
form-scoped tables with no FK path into `entity_records`. Promotion
to an entity is an explicit FSM action (`PROMOTE_FORM_TO_ENTITY`),
which means:

- A malformed submission can't poison your entity data.
- Files attached to a submission aren't silently copied into
  `entity_files` — the promoter has to opt in per field.
- The audit trail stays clean: every entity write has a named
  transition behind it.

## Forms v2 at a glance

Forms run on the v2 schema:

- **Multi-page** — a form is a list of `pages`, each owning a
  subset of `fields`. Page navigation is linear by default.
- **Conditional routing** — per-page JSONLogic rules can skip,
  branch, or short-circuit the stepper.
- **File uploads** — 20 MiB cap per attachment, scanned in three
  layers (MIME magic → VirusTotal → LLM content check). The submit
  handler claims attachments atomically at the same transaction
  as the submission INSERT.
- **Themes** — every form inherits the tenant's brand tokens;
  per-form overrides can pin a different theme or (when the
  feature flag is on) inject sanitized custom CSS.
- **Entity field picker** — author a form by importing annotated
  entity fields in one click; labels, requiredness, and enum
  options carry over.

## Wire shape

Stored definitions are JSON. The renderer + validator share the
same zod-backed contract on both sides of the wire. Old v1 rows are
read-shimmed into v2 on every read, and every write emits v2, so
you never have to migrate by hand.

## Public submission API

Anonymous (token-keyed) submissions go through:

```
GET  /api/v1/public/forms/:token             # fetch the form schema
POST /api/v1/public/forms/:token/submit      # submit a payload
```

The token is what the admin shell shows you when you publish a form
— the form's slug stays admin-side and is never exposed publicly.
Submit body is `{ "payload_json": { /* keyed by field key */ } }`.

The success response is:

```json
{
  "submission_id": "01HXY…",
  "form_definition_id": "01HXX…",
  "definition_version": 7,
  "processing_state": "received",
  "submitted_at": "2026-05-03T18:14:22Z",
  "pdf_download_url": "https://tenant.fly.dev/api/v1/public/attachments/…?sig=…&exp=…"
}
```

`pdf_download_url` is a 15-minute HMAC-signed URL pointing at the
PDF that was rendered for this submission. It's **omitted** when:

- the form has no PDF template attached, or
- the per-form `pdf_on_submit` flag is off, or
- the render attempt failed (the submission itself still
  succeeded — render failures don't block ingestion).

A `--template nextjs` consumer app scaffolded with the
[fy CLI](/docs/cli/commands#fy-init-name-template-kind) renders the
download URL as a one-tap "Download your signed copy" link
when present.

## PDF templates

Forms can carry a **PDF template** that gets rendered with each
submission. Two kinds:

- **AcroForm** — start from an existing fillable PDF; FastYoke
  fills the form fields from your form payload, embeds signature
  images as native PDF widgets, and flattens the result.
- **Overlay** — start from any PDF; place value/signature regions
  on the page coordinates you choose. Multi-page overlays and
  per-submission signatures are supported.

Both paths are deterministic: the same `payload_json` re-renders to
a byte-identical PDF. Templates are configured per form in the
admin shell's Forms Builder.

::callout{type="info"}
Builder access requires the `forms.create` or `forms.update` permission. See [Builder gating](/docs/permissions/builder-gating).
::

::callout{type="info"}
Need legally-binding signatures on a form? See [E-signatures](/docs/esign).
::

Form submissions emit `form.submitted` outbound webhooks;
see [Webhooks → Managing subscriptions](/docs/integrations/webhooks#managing-subscriptions)
for the subscription model and REST surface.
