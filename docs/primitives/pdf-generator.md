---
title: PDF Generator
description: Render a typst template against an entity's payload and attach the resulting PDF to a field.
---

# PDF Generator

The PDF Generator primitive renders a typst-source template using the entity's payload as the substitution context, writes the resulting PDF to storage, and attaches the new file id to a field on the entity. It runs asynchronously so the originating transition is never blocked by typst compilation.

## Configuration

| Field | Type | Description |
|---|---|---|
| `template` | string | Full typst source. Use `{{entity.field}}` substitutions to interpolate payload values. |
| `attach_to_field` | string | The entity field that receives the resulting `file_id`. |
| `filename_template` | string | Filename template, also Mustache-style (e.g. `quote-{{quote_number}}.pdf`). |

## Example: Render a quote PDF when a Quote is sent

```toml
[[bindings]]
primitive = "fastyoke.pdf-generator"
trigger = { kind = "on_transition", board = "Quote", event = "send_quote" }
config = {
  template = """
    #set page(\"us-letter\")
    = Quote {{quote_number}}
    Customer: {{customer_name}}
    Total: ${{total}}
  """,
  attach_to_field = "quote_pdf",
  filename_template = "quote-{{quote_number}}.pdf"
}
```

When the `send_quote` transition commits, the engine renders the typst source against the Quote's payload, uploads the PDF to tenant-scoped storage, and writes the resulting `file_id` to `Quote.quote_pdf`.

## When to use it

- Customer-facing documents that need precise typography (quotes, invoices, statements).
- Compliance artifacts produced as a side effect of a transition (signed agreements, audit reports).
- Anything else where the same template fills with per-entity values.

## What it can't do

- Render synchronously and surface the PDF in the transition response. The render is asynchronous to keep transitions fast — the field is populated when the render finishes.
- Reach over typst's sandbox. Templates have no host-system access and no network access.
- Exceed your tenant's daily render cap. The same anti-abuse cap that covers Forms typst renders applies here.
- Attach the PDF to email automatically. Pair PDF Generator with [Notifier](/docs/primitives/notifier) and reference the attached `file_id`.
