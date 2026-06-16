---
title: 'Yoke Architect — A Thesis'
description: 'Why we built an app composer, not a configurator.'
ogTitle: 'Yoke Architect — A Thesis'
ogDescription: 'Apps as data. An opinionated framework for building real software on FastYoke, with an ode to Haskell.'
---

::marketing-section{band="white" maxWidth="3xl"}
# Yoke Architect

**A Thesis on Apps as Data**

There are two ways to build software for a business. The first is the way most no-code platforms do it: a graphical editor mutates hidden imperative state, and your "app" is whatever the canvas happens to be displaying. The second is the way we built Yoke Architect: your app **is** a value. A description. A document you can read, version, diff, copy, paste into a brief, hand to an AI, regenerate, lint, or compile to two completely different outputs.

This document explains the second way, why we chose it, and what it costs us not to.
::

::marketing-section{band="gray" maxWidth="3xl"}
#title
## What a Yoke App actually is

#default
A **Yoke App** — what FastYoke&trade; calls a multi-board declarative application — is a single document. Internally we call it a YokeDoc. It has:

- A handful of **boards** — finite-state machines, each bound to an entity (an Order, a Patient, a Work Order, a Bed Request).
- **States**, **transitions**, optional **guards** (sandboxed predicates), and optional **engine primitives** bound to those transitions (schedule, notify, escalate, render PDF, aggregate, approve).
- **Pages** that render the boards — list views, detail templates, related-record cards.
- **Bridges** to other Yoke Apps in your tenant (your CRM customer profile shows the work orders from your Field Service Yoke; no code).
- A **tier** — free, Pro-trial, or paid.
- An optional **engine manifest** declaring what extra capabilities the app needs.

That's it. It is one tree of data. It has no methods. It does not "run." It is *applied* — by an interpreter that knows how to make it real.

This sounds like a small distinction. It is not.
::

::marketing-section{band="white" maxWidth="3xl"}
#title
## What Yoke Architect is

#default
**Yoke Architect** is the FastYoke surface that builds Yoke Apps. Open the Studio, choose Yoke mode, and you land in a wizard whose first decision is where to start. Five starting points are on the roadmap; they ship in waves:

::feature-grid{columns="2"}
  ::feature-card{title="Blank" summary="An empty Task board to grow from. Available now."}
  ::
  ::feature-card{title="From a vertical" summary="A curated recipe from our library: independent auto shop, hospital patient throughput, field service, sales pipeline, compliance program, inventory operations, general ledger. Each one is a real Yoke App, not a wireframe. Rolling out next."}
  ::
  ::feature-card{title="Clone an installed Yoke" summary="Start from one you already use. Rolling out next."}
  ::
  ::feature-card{title="Promote an app" summary="Take an existing single-board app you've built and reshape it into a Yoke; we wrap your boards and pages into the draft for you. Rolling out next."}
  ::
  ::feature-card{title="From a brief" summary="Type one sentence describing what you want; an AI drafts the full Yoke for you to edit. Rolling out next, Pro+ only."}
  ::
::

After the entry point, you edit boards, pick branding, choose a tier — free or Pro-trial — and click Save & Activate. The wizard never asks you to write code. It also never lies to you about what it's doing: the draft you see is the literal document that gets installed.

For the FastYoke engineering team, the same wizard is engineered to also produce a **paste-able patch**: the same YokeDoc compiles to Rust constants, installer and uninstaller functions, JSON assets, integration tests, and a customer doc page — everything needed to ship the app as a public marketplace listing. Both outputs — your tenant's database, or a unified diff against our codebase — come from the same description walked once by the same interpreter; only the writer at the end of the walk differs.

<blockquote class="yugabyte-quote">Stay with that sentence. It is the entire thesis.</blockquote>
::

::marketing-section{band="gray" maxWidth="3xl"}
#title
## Apps as data

#default
The decision underneath Yoke Architect is older than no-code, older than software, older than computers. It is the decision Lisp made in 1958, the decision Haskell made in 1990, the decision Terraform made when it separated `plan` from `apply`. **Programs are values. Effects are explicit. Composition is the primary verb.**

A YokeDoc is a value. Two YokeDocs are equal if their fields are equal. You can compare them, hash them, serialize them, snapshot them, fork them. You can hand one to a junior engineer and they can read it without running it. You can hand one to a language model and it can produce another one, and you can compare the two. You can save every revision a tenant has ever authored in an append-only history ledger and replay any of them on a fresh database.

The validator is a function. Given a YokeDoc, it returns either OK or a structured list of what is wrong — never mid-state, never partial, never with side effects. We can run it on the client, on the server, on the LLM's output, and on the recipe library at build time. It is the same function each time, and it will produce the same answer each time.

The materializer is an interpreter. It receives a YokeDoc and a mode — "install this into the calling tenant" or "emit a patch against our codebase" — and it walks the document once, structurally, to produce its result. The mode is a tagged enum. Adding a third mode (lint, document, simulate) is purely additive; the document never knows.

The boards are categories. States are objects, transitions are morphisms, guards are composition constraints. Self-loops are identity. Two transitions compose if the first's target equals the second's source. Cancellation is not a state, because cancellation is not a morphism — it is a runtime override from outside the category, modeled as such throughout the system. None of this is decoration. It is what made the FSM engine simple enough to be correct.

Engine primitives are typed effects. A binding declares "fire this primitive at this trigger with this configuration." It does not declare *how* the primitive runs. The runtime threads tenant context through the primitive; the binding is pure data. This is the algebraic-effects pattern you would see in OCaml 5 or Koka, in a Rust framework that does not have first-class effects, dressed up to feel native.

The vertical registry is a homoiconic value space. Each recipe is a Yoke App description sitting next to its metadata. The registry can sample from itself — when you ask an AI to draft a Yoke from a brief, the AI's prompt includes three randomly chosen recipes as examples of what a valid description looks like. The library teaches the model. The model's output goes through the same validator the library entries pass at build time. The loop closes.
::

::marketing-section{band="white" maxWidth="3xl"}
#title
## What this gets you

#default
Most no-code platforms cannot reproduce your app from a description, because there is no description. They can show you what your app looks like; they cannot tell you what it *is*. Yoke Architect can answer:

- **What changed between version 4 and version 7 of my Yoke?** (Diff two YokeDoc revisions in the history ledger.)
- **What does my Yoke do on the order-paid transition?** (Read the bindings on that transition.)
- **Can I export my Yoke, hand it to my partner, and have them install it on their tenant?** (Yes — clone-by-export is built in.)
- **Can I send my Yoke to an LLM and ask it to add a returns-and-refunds board?** (Yes — the validator catches the model's drift, the wizard surfaces the model's warnings, the human edits before saving.)
- **Can I, as a FastYoke engineer, take a tenant-authored Yoke and ship it back into our marketplace as a curated recipe?** (Yes — the export helper is the inverse of the materializer; round-tripping is a tested property.)
- **If a primitive scheduler accidentally fires a million times, can I bound it?** (Yes — primitives carry rate limits, trigger budgets, and self-loop guards as first-class concerns, not patched-in checks.)

<blockquote class="yugabyte-quote">None of this requires a rewrite. None of it requires a "Yoke 2.0." It is what falls out of treating the app as a value in the first place.</blockquote>
::

::marketing-section{band="gray" maxWidth="3xl"}
#title
## An ode to Haskell

#default
Yoke Architect is not written in Haskell. It is Rust on the server and TypeScript on the client. Our team does not write monad transformer stacks at lunch. But the *attitude* about programs — that they should be values you can reason about, that effects should be explicit, that composition should be the primary verb, that the pure region should be as large as possible and the imperative shell as small — that attitude is Haskell's, and Lisp's before it, and the lambda calculus's before that.

When we run into a design wart, our instinct is to make the system *more functional*: more append-only, more declarative, more composable, fewer hidden mutations. When we found a place where our schema versions were updated in place rather than appended, we filed it as wrong and put a fix on the books, because that is what the architecture demanded — even though the in-place update worked correctly. When we needed an LLM in the loop and could not make it deterministic, we did not try to fight the non-determinism; we put the model behind the same pure validator the rest of the system uses, and treated its output as an oracle to be checked, not an authority to be trusted.

<blockquote class="yugabyte-quote">We did not call any of this "functional programming" during the design. We called it "the simplest thing that could possibly work." That is usually a tell.</blockquote>

This is the architecture we want to put under FastYoke for the long run. Yoke Architect is the first product to expose it. Every Yoke App you build with it is a value — yours, ours, the AI's, and the next engineer who reads your code's. We borrowed the soul of a language, not its syntax. We think you will feel it the first time you ship a Yoke from a one-sentence brief and the LLM produces something you can actually read.
::

::marketing-section{band="white" maxWidth="3xl"}
**Try Yoke Architect.** Open your Studio, switch to Yoke mode, and start with any of the five entry points. If you build something interesting, [tell us](mailto:hello@fastyoke.io). If you build something boring, that's a good sign — it means the architecture got out of your way.
::
