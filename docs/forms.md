# Forms

> Build public or authenticated forms that route submissions into entities, jobs, or both.

# Forms

Forms are FastYoke's typed ingress layer. They collect data from
anyone (or anyone you've authenticated), validate against a schema
the builder owns, and feed submissions into your workflow via
declarative actions.

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
