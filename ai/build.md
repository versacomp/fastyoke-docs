---
title: Build with AI — Yoke Architect and AI Designer
summary: FastYoke Lane 1 AI — describe apps and workflows; get readable artifacts you can diff, export, and hand to engineers. Yoke Architect composes Yoke Apps; AI Designer extends FSMs and marketplace apps.
order: 6
---

::hero{eyebrow="Build with AI" title="Descriptions you can read — not configs you can't" tagline="Lane 1 AI on FastYoke generates **values**: YokeDocs, workflow specs, extension handoffs. Every LLM output passes the same validators as human-authored work. You edit before save; you export when you're ready to leave."}
::

::marketing-section{band="white" maxWidth="6xl"}
#eyebrow
Surfaces

#title
Three builder surfaces

#default
::feature-grid{columns="3"}
  ::feature-card{title="Studio wizard" icon="forms" accent="cyan" summary="On-rails and deterministic — pick layout, brand, entities, ship a themed extension. Same picks → same bundle every time. Not LLM-first."}
  ::cta-button{to="/docs/apps/studio-wizard" variant="secondary" size="sm"}
  Wizard docs
  ::
  ::
  ::feature-card{title="AI Designer" icon="field-service" accent="sky" summary="Natural language → FSM transitions, guards, and multi-page extension specs inside the Advanced App Builder. PII-scrubbed prompts; sandboxed guards at runtime."}
  ::cta-button{to="/docs/apps/advanced" variant="secondary" size="sm"}
  Advanced App Builder
  ::
  ::
  ::feature-card{title="Yoke Architect" icon="project-tracker" accent="emerald" summary="Compose multi-board Yoke Apps as a single document — states, guards, pages, bridges. LLM drafts from a one-sentence brief; you edit in the wizard."}
  ::cta-button{to="/yoke-architect" variant="secondary" size="sm"}
  Read the thesis
  ::
  ::
::
::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
AI Designer

#title
Extend workflows without losing the exit clause

#default

The **Advanced App Builder** is the enterprise / ISV delivery surface:

- Author engineering specs by hand **or** with AI assistance.
- Track nav and page health across a multi-page extension.
- Download a **handoff zip** for an external SDK developer when you want Rust/React ownership outside the visual tools.

AI Designer sits inside that surface — it accelerates spec authorship; it does not replace your ability to export compiled rules and frontend repos. External LLM calls scrub PII by default; see the [subprocessor list](/docs/security/subprocessors).

::cta-button{to="/developers" variant="primary" size="md"}
Developers & ISVs
::
::

::marketing-section{band="white" maxWidth="3xl"}
#eyebrow
Yoke Architect

#title
From a brief to a YokeDoc

#default

**From a brief** is the LLM entry point: type one sentence, receive a full Yoke draft, run it through the same validator as curated vertical recipes, edit boards and branding, then Save & Activate.

The vertical recipe library **teaches** the model what valid YokeDocs look like — homoiconic examples in the prompt, deterministic validation on the output.

::cta-button{to="/yoke-architect" variant="primary" size="lg"}
Yoke Architect thesis
::

::cta-button{to="/studio" variant="secondary" size="md"}
Open Studio
::
::

::marketing-section{band="gray" maxWidth="3xl"}
#eyebrow
Contrast

#title
Lane 1 vs Lane 2

#default

| | **Build (this page)** | **Operate ([Yoke](/yoke))** |
|---|---|---|
| **Audience** | Builders, ISVs | Owners, dispatchers |
| **Input** | Briefs, specs, natural language | Event log + schema aggregates |
| **Output** | YokeDoc, extension zip, FSM JSON | Inbox briefs + digest email |
| **Approval** | Human edits before save | Human approves before apply |

::cta-button{to="/ai" variant="ghost" size="md"}
← All AI capabilities
::
::
