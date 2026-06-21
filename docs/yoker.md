---
title: Yoker (AI assistant)
summary: A RAG-powered chat assistant in the right-toolbar dock. Ask natural-language questions grounded in your entity records and text attachments. On-VM embeddings, no external API calls.
order: 1
---

# Yoker

Yoker is FastYoke's AI assistant — a RAG-powered chat that lives
in the right-toolbar dock alongside Messaging. Ask natural-language
questions about your tenant's entity records and their text
attachments; the platform retrieves the most relevant chunks and
synthesizes an answer.

Two things make Yoker different from a generic chatbot wired to
your data:

1. **Embeddings run on your VM.** The platform never sends your
   data to OpenAI, Anthropic, or any other external embedding
   provider.
2. **Conversations are ephemeral.** The server doesn't persist
   your chat history; the client keeps it during a session and
   forgets it after.

## How to enable Yoker

Two paths:

- **Enterprise / Fleet** — Yoker is **included**. No add-on
  needed.
- **Pro / Team** — Yoker requires the paid `yoker` add-on at
  **$299/mo** (rates as of June 2026; check the platform's
  pricing page for current rates).

Hobby / Solo tiers cannot use Yoker — no add-on path is offered.

Without entitlement, every Yoker endpoint returns 403 with a
`yoker_not_entitled` error code (see
[API reference](/docs/yoker/api-reference)) and a CTA pointing at
the add-on activation flow.

## The dock

Yoker lives in the right-toolbar dock — the same surface
[Messaging](/docs/messaging) uses, different tab. The dock is
persistent across pages; chat stays open while you navigate.

Conversations are ephemeral. The platform doesn't store your chat
history server-side — the client maintains the conversation during
a session, the server forgets every turn after answering.

## What Yoker knows today

Two ingestion sources are shipped:

- **Entity records.** Every entity record's `data_payload` is
  rendered to text, chunked (sliding window 1500 chars / 200
  overlap), embedded, and stored. Write-through on every entity
  create or update — Yoker reflects new records within seconds.
- **Entity file attachments — text, CSV, and Markdown only.**
  Uploaded files with `text/*`, `text/csv`, or `text/markdown`
  MIME types are parsed UTF-8 lossy, chunked, and embedded with
  the source filename prefixed for context. PDF, Word, and Excel
  attachments are **not parsed** today (see deferred list).

## Privacy: on-VM, no external API calls

Yoker runs all embeddings locally on the tenant's VM using ONNX
runtime (`tract-onnx` + `all-MiniLM-L6-v2`, 384-dim). The
platform never sends your data to external embedding providers
(OpenAI, Anthropic, Voyage, or others).

The vector store is the tenant-scoped `rag_chunks` table — chunks
live with the tenant data and are isolated by the platform's
prime directive. See [Tenant scoping](/docs/developers/tenant-scoping)
for the full contract.

## Backfill

When you enable Yoker on a tenant with existing data, the corpus
starts empty until you run a backfill:

```http
POST /api/v1/tenant/rag/backfill
```

The backfill walks every entity record and every text/CSV/Markdown
attachment, re-embeds, and upserts. Idempotent via `content_hash` —
running it twice is safe.

After the initial backfill, write-through keeps the corpus current
for entity records (create/update) and attachments (upload/delete).

## What's not in Yoker today

Documentary list of explicit non-features:

- **PDF, Word, and Excel attachment parsing** — only text/CSV/
  Markdown attachments are indexed today.
- **Streaming chat responses** — responses arrive as a single
  complete message; no token-by-token streaming.
- **Persistent conversation history** — chat history is
  ephemeral and client-side only.
- **External-embedding APIs** — Yoker doesn't support sending your
  data to OpenAI / Anthropic / other embedding providers;
  everything stays on the VM.
- **Platform documentation** as a corpus — Yoker can't answer
  "how do I configure forms?" from these docs today.
- **Workflow configuration** and operational history — Yoker
  can't answer "what FSM transitions fired yesterday?" from job
  audit logs today.

## See also

- [API reference](/docs/yoker/api-reference) — `/assistant/ask`
  and `/rag/backfill` endpoint shapes plus the gating 403.
- [Entities](/docs/entities) — where the records come from.
- [Messaging](/docs/messaging) — the dock neighbor.
