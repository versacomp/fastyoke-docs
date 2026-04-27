# Advanced App Builder

> Enterprise SPA-delivery surface — artifacts, health, handoff zip, and the template / LLM-authored extension generators.

# Advanced App Builder

Enterprise-tier tenants see an **Advanced** tab on every app at
`/admin/apps/:id/advanced`. It's a thin shell around the app's
`advanced_config_json` envelope and a handful of delivery actions.

This page walks through the surface. For the raw spec see
`Advanced-App-Builder-Enterprise-Spec.md` at the repo root; this
doc is the admin-facing rewrite.

## What lives here

| Section | Purpose |
|---|---|
| **Experience strategy** | Pick workspace pages (Path 1) vs extension SPA (Path 2). Drives AI Designer proposals. |
| **Artifacts** | Two markdown documents — `engineering_spec_md` and `feature_phases_md` — that describe the app's SPA shape. Authored by the admin (or the AI Designer). |
| **Nav + page health** | Every nav entry classified as `ok` / `missing_page` / `orphaned_extension`, with per-row deep links. |
| **Handoff zip** | One-click download with both markdowns + a manifest stub + a README for an external SDK developer. |
| **Generate extension** | Produce a working extension bundle from the artifacts — template or LLM-authored. |

## Authoring artifacts with AI

The Advanced page's two markdown editors (`engineering_spec_md`
+ `feature_phases_md`) feed the bundle generator. Drafting them
from a blank page is tedious, so an **Author artifacts with AI**
button above the editors calls Claude Opus 4.7 with a pinned
spec-writing prompt and populates both fields for you to review
and edit before **Save**.

- Requires the same Anthropic key as the bundle generator
  (`llm.anthropic_api_key`). The button is disabled with a
  tooltip when no key is stored — wire one up at
  [`/admin/settings/llm`](/admin/settings/llm).
- Each call ≈ $0.05. Metered against
  `ResourceType::LlmAuthorArtifacts` (distinct from
  `LlmGenerate`) so spend reports can distinguish spec
  authoring from bundle generation.
- **Overwrite protection:** if either editor already has
  content, clicking the button toggles an inline confirm
  strip. A second click (or Cancel) proceeds / aborts. No
  blocking modal — the text just warns.
- **Nothing persists on the LLM call itself.** The response
  goes into the textareas as draft; the admin clicks **Save**
  to write them to `apps.advanced_config_json`.
- **If you have a half-authored draft**, the button passes your
  existing content to the prompt as an `<existing_*>` XML
  block — the model refines rather than blank-slates.

Failure modes mirror the bundle generator: 422 for missing key
or invalid LLM JSON, 502 for upstream transport errors, every
call is metered regardless.

## Generate extension

The generator turns your `engineering_spec` + `feature_phases`
markdown into a scan-clean ES-module extension bundle the host
knows how to load. Two synthesis modes, three delivery modes:

### Synth modes

| Mode | Output | When to use |
|---|---|---|
| **Template** | Deterministic stub — same inputs → identical bytes. Embeds the two markdowns as `<pre>` blocks and prints a disclaimer. | First pass; scaffolding; CI / automation. Runs offline, costs $0. |
| **LLM-authored** | Claude Opus 4.7 authors a real React functional component from your artifacts. Every call is non-deterministic. | Ready to ship a working UI. ≈ $0.05 per call. |

Pick the mode with the segmented control above the Download /
Install buttons. LLM mode is disabled until an Anthropic key is
stored — see [Setup](#setup-for-llm-mode) below.

### Delivery modes

| Mode | Behavior |
|---|---|
| **Download** | Streams a three-file zip: `manifest.json`, `index.mjs`, `README.md`. Nothing persists server-side beyond a `delivery.last_generated_at` timestamp. |
| **Install** | Inserts a `tenant_extensions` row with `is_active = 0`. The admin reviews + activates manually from the [Extensions page](/admin/extensions). |
| **Preview** *(LLM only)* | Returns the authored source as JSON and opens a modal. No persistence. Used internally by the Install button in LLM mode — you don't call it directly from the UI. |

### LLM install flow (preview-then-confirm)

Clicking **Install** while `synth = llm` runs a two-step flow:

1. `POST .../generate-extension` with `mode: "preview", synth: "llm"`.
2. Modal opens with the authored source in a scroll-locked `<pre>`.
3. **Cancel** discards and closes the modal. **Confirm install**
   fires a **second** LLM call with `mode: "install", synth: "llm"`
   that actually writes the `tenant_extensions` row.

The double-LLM-call (≈ $0.10 per install flow) is deliberate —
each confirm re-rolls the bundle, so admins who don't like the
first preview can Cancel → Install → preview again for a fresh
attempt. If you want the same source twice, use **Download** after
Preview-then-Confirm and re-install locally via the Extensions
page.

### What you get out

Regardless of mode, the manifest advertises exactly one page at
`/ext/<id>/<app-slug>` and `required_scopes: ["data:read"]`. Both
are **pinned** server-side — editing them requires downloading,
editing the manifest, and re-uploading through the Extensions
page.

Installed rows are always `is_active = 0`. The admin must
explicitly activate from `/admin/extensions`. Deliberate: LLM
output is untrusted input and the scanner + heuristic validator
are defenses-in-depth, not guarantees.

## Setup for LLM mode

LLM-authored generation needs a dedicated Anthropic API key,
stored separately from the AI Designer's primary LLM config so
OpenAI / Gemini tenants can opt in without reconfiguring their
chat.

Configure at [`/admin/settings/llm`](/admin/settings/llm):

1. Scroll to **Anthropic API Key — code generator**.
2. Paste an `sk-ant-…` key.
3. **Save Settings**. The LLM-authored toggle on the Advanced
   App Builder becomes enabled immediately.

The primary **API Key** field above is independent. You can run
OpenAI / Gemini / DeepSeek / custom for AI Designer chat while
still having an Anthropic key on file for the code generator.

### What's pinned

The LLM synthesis path is not tenant-configurable beyond the
api_key. Pinned to:

- Provider: `anthropic`
- Base URL: `https://api.anthropic.com`
- Model: `claude-opus-4-7`
- Required scopes on the emitted manifest: `["data:read"]`
- Bundle size cap: 128 KiB

Code-generation quality is load-bearing, and cheap models
produce unparseable JS. Changing any of these is a source-level
edit.

## Failure modes

The generator surfaces four distinct HTTP responses — all
admin-readable in the red error banner below the buttons:

| Status | Cause | Fix |
|---|---|---|
| `400 Bad Request` | Blank artifacts; `synth` not in `template`/`llm`; `mode` not in `download`/`install`/`preview`. | Fill in the markdown editors above. |
| `422 Unprocessable` | LLM mode: missing Anthropic key → error points at `/admin/settings/llm`. Or: validator rejected the authored bundle (forbidden token, missing import, size cap). | Add the key, or re-roll via Install (cheaper to retry than to debug). |
| `502 Bad Gateway` | Anthropic API returned an error or timed out. | Transient — try again in a minute. Check your Anthropic account is in good standing. |
| `500 Internal` | Our bug. | File an issue with the request timestamp + tenant id. |

Every LLM-mode call is metered against
`ResourceType::LlmGenerate` in `utility_ledger` **regardless of
outcome** — a 422 validator reject or 502 transport failure
still costs. Log-aggregation carries the full detail (actor,
model, bundle bytes, violation count, outcome) via
`tracing::info!` structured fields.

## Cost expectations

Rough estimate at ship: $0.05 per LLM call, scaled by your
organization's `wholesale_discount_multiplier`. The Install
flow uses two calls (preview + confirm), so ≈ $0.10 per
installed extension. Template mode is free (no upstream).

Tunable — `ResourceType::LlmGenerate::base_cost` in
`backend/src/metering.rs` is the canonical value; adjust once
you have real spend data.

## Real-data extensions (SDK 0.2.0+)

Generated bundles are no longer limited to static markdown. The
template and LLM-authored paths both have access to the full
[SDK hook surface](/docs/sdk/reference#react-data-hooks), so an
extension can list entity records, render FSM workflow state,
and mutate tenant data through the exact same backend API the
admin shell uses.

### What this means for generated bundles

- **Default template** — the `index.mjs` scaffold now renders
  live panels (active workflows, recent jobs) instead of a static
  artifact preview. `npm run build:sdk` ships these
  hooks in the package; the template imports them directly.
- **LLM-authored** — the system prompt enumerates every shipped
  hook with signatures + three worked examples (paginated
  `useEntities`, `useJob` + ``, transition
  button). The model is forbidden from emitting `const SEED_*` /
  `const FAKE_*` stubs — prompt-level enforcement; slips get
  caught in human review at the Preview modal.
- **Scope inference** — a pre-install analyzer greps the
  generated source for hook names and widens the manifest's
  `required_scopes`. A bundle that calls `useCreateEntity` lands
  with `["data:read", "data:write"]`; `useTransitionJob` adds
  `"workflow:execute"`; `useCancelJob` adds `"workflow:admin"`.
  The admin sees the inferred set in the amber scope-disclosure
  strip on the preview modal before confirming.

### Realtime auto-refresh

Every read hook subscribes to the provider's shared WebSocket by
default. `useEntities('widget')` refreshes whenever another
session creates, updates, or deletes a widget; `useJob(id)` +
`` redraw on every transition — no
polling, no backend changes on your end.

Opt out per hook when a view is deliberately snapshot-style:

```tsx
useEntities('widget', undefined, { realtime: false });
```

See the [CRUD UI recipe](/docs/recipes/sdk-crud-ui) for a full
list + create + update + delete page using these hooks.

## Related

- [Apps overview](/docs/apps) — the non-advanced surface.
- [Extensions](/admin/extensions) — where installed bundles
  land and are activated.
- [Settings → LLM](/admin/settings/llm) — where the Anthropic
  key is stored.
- [SDK reference](/docs/sdk/reference) — the hooks + components
  the LLM has in its prompt context.
- [CRUD UI recipe](/docs/recipes/sdk-crud-ui) — full CRUD page
  using the 0.2.0 hook surface.
