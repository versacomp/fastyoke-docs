---
title: Yoker — RAG assistant grounded in your tenant data
summary: Yoker is FastYoke's docked AI assistant. Ask natural-language questions about entity records and text attachments; retrieval runs on-VM with ONNX embeddings. Enterprise included; Pro and Team via add-on.
order: 5
---

::hero{eyebrow="Enterprise · Add-on" title="Ask your business data — retrieval stays on your VM" tagline="Yoker lives in the right-toolbar dock next to Messaging. It retrieves the most relevant chunks from your **entity records** and **text attachments**, then synthesizes an answer with citations. Embeddings run on your VM — tenant data is not sent to OpenAI or Anthropic for search."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Yoker answers. Yoke proposes.

| | **Yoker** | **Yoke** |
|---|---|---|
| **Job** | Answer questions about what you already stored | Propose workflow and schema improvements |
| **Output** | Chat reply + source chips | Brief in an inbox you approve |
| **Retrieval** | On-VM semantic search (RAG) | Template matchers over event log aggregates |
| **Landing** | [Yoker docs](/docs/yoker) | [Yoke overview](/yoke) |

::

::marketing-section{band="gray" maxWidth="6xl"}
#eyebrow
Privacy

#title
1. What makes Yoker different

#default
::feature-grid{columns="3"}
  ::feature-card{title="On-VM embeddings" icon="warehouse" accent="violet" summary="Record chunks are embedded with ONNX on your FastYoke VM. Vector search runs in Rust against tenant-scoped rag_chunks — no external embedding API."}
  ::
  ::feature-card{title="Ephemeral sessions" icon="forms" accent="cyan" summary="The server does not persist chat history. The client keeps the thread during your session; each turn is forgotten server-side after the answer."}
  ::
  ::feature-card{title="Cited sources" icon="crm" accent="emerald" summary="Answers include used_sources chips so operators can verify which records grounded the reply — critical for regulated workflows."}
  ::
::
::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
Corpus

#title
2. What Yoker knows today

#lede
Retrieval expands in waves; these ingestion sources ship now.

#default
::feature-grid{columns="2"}
  ::feature-card{title="Entity records" icon="inventory" accent="amber" summary="Every entity record's data_payload is rendered to text, chunked (1500 chars / 200 overlap), embedded, and indexed per tenant."}
  ::
  ::feature-card{title="Text attachments" icon="project-tracker" accent="sky" summary="PDF, Word, and plain-text attachments on entity records are parsed, chunked, and indexed alongside records — ideal for SOPs and contract PDFs."}
  ::
::

::callout{type="info"}
Platform how-to docs and workflow config ingestion are on the roadmap as additional corpus sources. See the [Yoker docs index](/docs/yoker) for the current API surface.
::

::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Enable

#title
3. How to enable Yoker

#default

- **Enterprise / Fleet** — Yoker is **included**.
- **Pro / Team** — requires the **`yoker` add-on** at **$299/mo** (check [pricing](/pricing) for current rates).
- **Hobby / Solo** — no add-on path.

Without entitlement, Yoker endpoints return `403 yoker_not_entitled` with a CTA to activate the add-on.

::cta-button{to="/docs/yoker" variant="primary" size="lg"}
Read the Yoker docs
::

::cta-button{to="/docs/yoker/api-reference" variant="secondary" size="md"}
API reference
::
::

::marketing-section{band="white" maxWidth="3xl"}
#default

## Compliance workflows

Pair Yoker with [Compliance Yoke](/docs/compliance-yoke) for framework-readiness sweeps, then ask tenant-scoped questions about collected evidence — see [Lab M13](/docs/training/labs/M13-compliance-yoker) in the training curriculum.

::cta-button{to="/ai" variant="ghost" size="md"}
← All AI capabilities
::
::
