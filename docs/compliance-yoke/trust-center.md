---
title: Trust Center
summary: A tenant-controlled public posture page with an optional AI Q&A endpoint — published from an allowlist of controls, not by redacting a full export.
order: 5
---

# Trust Center

The Trust Center is the public-facing companion to the
Compliance Yoke. You publish a posture page describing the
controls you've implemented; prospects and auditors hit it
without needing a platform login.

## Allowlist model

The public posture is an **allowlist**. Nothing is published
until you explicitly add it. There is no "everything the Yoke
knows minus what you redacted" mode — the safer default is
"nothing leaves the platform unless the operator names it."

When you flip the Trust Center on, you configure:

- which framework(s) to publish,
- which controls per framework to show,
- a public slug (e.g. `acme-co`) at which the page is reachable.

A request for a Trust Center slug that has not been enabled
returns a 404. The platform never serves a partially-configured
posture page.

## AI Q&A

The Trust Center has an optional **AI Q&A** endpoint at
`POST /ai/answer`. A prospect asks a natural-language question
("Do you encrypt customer data at rest?") and the platform
answers grounded **only** in the published controls — not the
full Yoke, not internal evidence files, not unrelated tenant
data.

The AI Q&A endpoint is a per-tenant toggle on the Trust Center
config. Defaults to off. Turning it off pulls the answer
endpoint immediately; the published posture page itself
remains.

## What's not in the Trust Center today

- **Custom branding** — logos, colors, fonts on the public
  page.
- **Custom domains** — Trust Centers serve from the platform
  domain.
- **Posture-page embeds** — an iframe-friendly variant for
  embedding on your marketing site.
- **Scheduled posture-snapshot exports** — auto-emailing a
  snapshot to a list of recipients on a cadence.

## See also

- [Frameworks & readiness](/docs/compliance-yoke/frameworks) —
  the source of what you can publish.
- [API reference](/docs/compliance-yoke/api-reference) for the
  Trust Center config endpoints and the AI Q&A request shape.
