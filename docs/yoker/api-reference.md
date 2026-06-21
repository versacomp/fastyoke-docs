---
title: Yoker API reference
summary: REST endpoints for the Yoker assistant and the corpus backfill, plus the entitlement-gating 403 envelope and the deferred-API list.
order: 2
---

# Yoker API reference

This page covers the two REST endpoints Yoker exposes plus the
entitlement-gating response shape. Same surface is available to any
client with a tenant-scoped admin JWT and an active Yoker
entitlement.

## Authentication

Every endpoint below requires:

- A **tenant-scoped JWT** — the same token the rest of the
  platform uses. See [Authentication](/docs/auth) for how to mint
  and present one.
- An **active Yoker entitlement** on the tenant. See
  [Gating](#gating) below for what happens without one.

## Ask the assistant

```http
POST /api/v1/tenant/ai/assistant/ask
Content-Type: application/json
Authorization: Bearer <tenant JWT>
```

Request body:

```json
{
  "message": "Which customers are overdue?",
  "history": [
    { "role": "user", "content": "Show me the customer entity schema." },
    { "role": "assistant", "content": "It has fields: name, status, …" }
  ]
}
```

`history` is optional and client-managed — the platform doesn't
persist conversation state. Pass prior turns if you want
context-aware answers; omit them for a fresh question.

Response:

```json
{
  "reply": "Three customers are currently overdue: …",
  "retrieved_chunks": [
    { "source_kind": "entity_record", "source_id": "rec_…", "score": 0.83 }
  ]
}
```

`retrieved_chunks` reports the corpus chunks used to ground the
answer. Each chunk has a `source_kind` (`entity_record` or
`attachment`), a `source_id`, and a relevance score.

## Backfill the corpus

```http
POST /api/v1/tenant/rag/backfill
Authorization: Bearer <tenant JWT>
```

Sweeps every entity record and every text/CSV/Markdown attachment
in the tenant; re-embeds; upserts into `rag_chunks`. Idempotent
via `content_hash` — running it twice is safe and won't duplicate
chunks.

Response:

```json
{ "added": 142, "updated": 18, "skipped": 7 }
```

Run this after enabling Yoker on a tenant with existing data, or
after running a heavy data import. Day-to-day writes are kept
fresh by write-through on entity create/update and attachment
upload/delete.

## Gating

Calls fail closed when the tenant doesn't have an active Yoker
entitlement (Enterprise / Fleet tier OR active `yoker` add-on on
Pro+):

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "error": "yoker_not_entitled",
  "message": "Yoker requires the Enterprise tier or the $299/mo yoker add-on on Pro+."
}
```

Status: `403`. Same response shape for both `/assistant/ask` and
`/rag/backfill`. The admin shell renders this as a CTA pointing
at the add-on activation flow.

## Errors

Standard `AppError` envelope used elsewhere on the platform. The
Yoker-specific surface:

- **`403`** — tenant doesn't have Yoker entitlement (see
  [Gating](#gating)).
- **`409`** — LLM upstream returned an unexpected shape; safe to
  retry.
- **`503`** — embeddings model not loaded on this VM. The
  operator owes `model.onnx` + `tokenizer.json` under
  `backend/assets/rag/`. Surface this error to the operator
  rather than retrying.

See [Error envelope](/docs/developers/errors) for the full
status-code table and worked examples.

## What's not in the API today

Same list as the [overview page](/docs/yoker), scoped to API
impact:

- No streaming endpoint — responses arrive as one complete
  message.
- No conversation-resume endpoint — history is client-managed.
- No external-embedding configuration endpoint — embeddings are
  on-VM only.
- No per-source ingestion endpoint — backfill is all-or-nothing
  across entity records and text attachments.
