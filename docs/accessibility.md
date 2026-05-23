---
title: Accessibility
summary: WCAG 2.1 AA enforced in CI, theme contrast validated on save, focus-trap modal primitive — the architectural pieces that clear institutional procurement.
order: 10
---

# Accessibility

FastYoke targets **WCAG 2.1 Level AA** as a platform invariant —
not a per-tenant opt-in. The goal is a clean Voluntary Product
Accessibility Template (VPAT) for institutional and enterprise
procurement, with compliance engineered into the CI pipeline and
the theme-save path rather than retrofitted.

This section documents what's live today and what's planned.

::section-cards{section="accessibility"}
::

## What's live today

| Piece | Status | Notes |
|---|---|---|
| Axe-core CI gate | **shipped, advisory** | `.github/workflows/a11y.yml` runs Playwright + `@axe-core/playwright` across 12 routes on every PR. Critical/serious WCAG 2.1 AA violations surface in the PR comment but don't block merge yet — the VPAT generator flips the gate when shipped. |
| Theme contrast validation | **shipped, blocking** | Branding save returns a structured 422 when `primary_text↔primary` or `sidebar_text↔sidebar_bg` falls below 4.5:1. Auto-suggests the nearest compliant shade. Bypass writes an audit row. |
| ARIA announcer | **shipped** | `useA11yAnnouncer` Zustand store + `<A11yLiveRegion />` mounted at the admin shell. Two static regions (polite + assertive) with the clear→write→clear drain pattern. |
| Modal focus-trap primitive | **shipped** | `<Modal />` wraps `@radix-ui/react-dialog`. Focus trap, Escape dismissal, focus restoration, `aria-labelledby` / `aria-describedby` wiring all baked in. `<SkipToContent />` mounted as the first tabbable element of the admin shell. |
| `aria-invalid` sweep | planned | Every controlled input binds the attribute to its current validation state. |
| VPAT generator | planned | In-CI generator that turns the rule-coverage matrix into a committed `docs/vpat/FastYoke-VPAT-2.5.md`. |

## Where the gate runs

The CI workflow boots a production build of the SPA via
`vite preview`, runs Playwright + `@axe-core/playwright` against a
fixed cycle list, and writes a JSON report. It currently scans:

- The marketing landing (`/`)
- The docs index (`/docs`)
- The login page (`/login`)
- Every primary admin route under `/admin/*` — Dashboard,
  Workflows, Jobs, Entities, Pages, Forms, Settings, Branding,
  Plan & Billing.

Admin routes are auth-stamped against a fake JWT in localStorage
(no real backend in CI); API calls are intercepted via
`page.route()` and served from `frontend/a11y/fixtures.ts`. This
keeps the audit job under two minutes and removes a backend
failure mode from the gate.

## Allowlist policy

Known-flake violations live in the repo-root
[`axe-allowlist.json`](https://github.com/versacomp/fastyoke2/blob/main/axe-allowlist.json)
with a hard 90-day expiry. The harness rejects expired entries
on load — there's no mute-and-forget path. Add an entry only
when the violation is genuinely outside our DOM (e.g. third-
party iframes) and pair it with a tracking ticket.

## Theme contrast on save

Branding settings (`/admin/settings/branding`) validate
role-paired colors before persisting. The two pairs we check
are `primary_text ↔ primary` (button-text-on-button) and
`sidebar_text ↔ sidebar_bg` (sidebar nav). Both must clear
4.5:1 per WCAG 1.4.3.

A failing pair returns a 422 with the structured body the UI
turns into the per-pair `Use {hex}` buttons:

```json
{
  "error_code": "theme_contrast_failed",
  "message": "Theme contrast does not meet WCAG 2.1 AA",
  "violations": [
    {
      "fg_var": "primary_text",
      "bg_var": "primary",
      "fg_hex": "#ffffff",
      "bg_hex": "#3b82f6",
      "computed_ratio": 3.68,
      "target_ratio": 4.5,
      "suggested_fg": "#1d4ed8"
    }
  ]
}
```

The "Bypass for staging" affordance writes an append-only row to
`theme_contrast_rejections` (platform DB, single migration
`0062`) so the compliance signal isn't lost when an operator
chooses to ship a non-compliant tenant for a staging env.

::callout{type="tip" title="Algorithm note"}
The nearest-compliant suggestion walks the **HSL lightness
  axis** in 1% steps, with fallback to the opposite direction
  when the primary pole is exhausted. The original spec text
  mentioned L*a*b*; pragmatic divergence: WCAG
  relative luminance is YCbCr-derived, not Lab — perceptual
  accuracy isn't the goal here, threshold-met-or-not is.
::

## The Modal primitive

Every modal in the SPA mounts through
`frontend/src/components/Modal.tsx`. The primitive owns:

- Tab cycling — focus stays inside the dialog while open.
- Escape dismissal — calls `onOpenChange(false)`, even when
  `dismissOnBackdropClick={false}`.
- Focus restoration — focus returns to whatever element opened
  the dialog, captured via `useLayoutEffect` before Radix's
  focus scope takes over (Radix's built-in restoration only
  works with `<Dialog.Trigger>`; we ship a fully-controlled API).
- Initial focus — Radix moves focus to the first focusable
  child by default; override with `initialFocusRef`.
- `aria-labelledby` + `aria-describedby` wiring via
  `<Modal.Description>`.

```tsx
import { Modal } from '@/components/Modal';

<Modal
  open={isOpen}
  onOpenChange={setIsOpen}
  title="Confirm"
>
  <Modal.Description>
    Are you sure you want to proceed?
  </Modal.Description>
  <div className="flex justify-end gap-2">
    <button onClick={() => setIsOpen(false)}>Cancel</button>
    <button onClick={handleConfirm}>OK</button>
  </div>
</Modal>
```

Hand-rolled `<div className="fixed inset-0">` modals are forbidden
for new code. The VPAT generator release will flip the axe gate
to assert "no top-level dialog DOM outside the primitive"; existing
16 hand-rolled sites migrate opportunistically.

## Announcer for non-visual signals

Surfaces with no in-place error UI (optimistic-UI rollbacks,
async-result toasts, validation announcements) push messages
through `useA11yAnnouncer.announce()`:

```ts
import { useA11yAnnouncer } from '@/store/useA11yAnnouncer';

const announce = useA11yAnnouncer((s) => s.announce);

await transitionJob(jobId);
// On success or error...
announce(`Job ${jobId} transitioned to ${nextState}`);
// or
announce('Card declined — please try a different payment method', 'assertive');
```

Two regions are mounted at the admin shell root: one
`aria-live="polite"`, one `aria-live="assertive"`. The store
queues messages with a 100ms write delay (so screen readers
catch the DOM mutation reliably) and a 3-second hold (so the
same text re-announces cleanly).

## VPAT readiness

The VPAT generator (planned) will produce `docs/vpat/FastYoke-VPAT-2.5.md` from
the live axe rule-coverage matrix plus a hand-curated
attestation file. Until then, the per-route CI report is the
authoritative source — every `main`-branch run uploads the JSON
artifact, and the [baseline snapshot](https://github.com/versacomp/fastyoke2/blob/main/docs/a11y/baseline-2026-04-26.md)
records the burndown target.
