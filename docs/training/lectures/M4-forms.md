---
title: Lecture M4 — Forms
summary: Why forms are zod-mirrored end-to-end, how anti-abuse works on public links, and the form-to-job submission pattern.
order: 5
---

# Lecture M4 — Forms

## The mental model

Forms are the boundary between the public web and your
tenant data. They run client-side in the browser, validate
the same shape the server validates, and (often) write
straight to an entity table or start an FSM job. The
discipline is **identical schemas on both sides** — what
the client validates is what the Rust server deserializes.

## Key concepts

- **Zod-mirrored schemas.** The frontend uses `zod`; the
  backend uses `serde`. The shapes must mirror each other
  — that's the whole game. A field renamed on one side
  but not the other is the bug class this discipline
  prevents.
- **Public-link submissions.** A form can be exposed at
  a stable URL with no auth. The platform issues a
  one-shot anti-abuse token + rate-limits the URL.
- **File uploads.** Validated by extension, size, and
  MIME. Stored as tenant-scoped blobs.
- **Form-to-job wiring.** Submitting a form can also
  start an FSM job — the form payload becomes the job's
  initial entity row.
- **Rate-limit + CAPTCHA.** Anti-abuse is layered:
  per-IP rate limit, then a CAPTCHA when the rate climbs,
  then a soft-block.

## Common pitfalls

- **Diverging schemas.** A field added on the frontend
  but not on the Rust struct → submission rejects with a
  400. Easy to debug — but easy to ship if you don't
  enforce the convention.
- **Re-using the same public link past its anti-abuse
  threshold.** The platform stops accepting; you'd
  rotate the link.
- **Authenticating a public link by adding a token
  query param.** Don't. Public means public. If you need
  auth, the form lives behind a session.

## Where to go next

- Lab: [M4 — Public-link form](/docs/training/labs/M4-forms).
- Reference: [/docs/forms](/docs/forms).
