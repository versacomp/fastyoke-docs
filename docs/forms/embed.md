---
title: Embed forms on your own site
summary: Drop a single script tag into any page to render a FastYoke form inline, with theme overrides and origin allowlists.
order: 20
---

# Embed forms on your own site

The embed feature lets you render any published FastYoke form directly on a
third-party website — your marketing site, a customer portal, or any static
page — without an `<iframe>` tag you manage yourself. A single `<script>` tag
loads the FastYoke embed SDK, which mounts the form in a sandboxed iframe
inside a closed Shadow DOM.

## Quickstart

### 1. Create an embed in the admin shell

Open the form you want to embed, then navigate to the **Embed** tab inside the
form builder. Configure the mode and any optional settings, then click **Save
embed**. The panel will show the snippet to paste.

### 2. Paste the snippet

Copy the generated snippet and paste it anywhere in your page's HTML where you
want the form to appear (for `inline` mode, place it in the exact spot; for
`modal`, `slide`, or `bubble` modes, placement is less critical because the
chrome renders in a fixed overlay).

```html
<script async src="https://app.fastyoke.io/embed.js" data-embed="em_AbCd1234567890EfGhIjKl"></script>
```

The `em_` prefix followed by 22 alphanumeric characters is the opaque embed ID
assigned when you save the embed. The snippet is shown — and copyable — from
the **Embed** tab after saving.

The script loads asynchronously and does not block page rendering.

## Embed modes

| Mode | Description | Tier |
|------|-------------|------|
| `inline` | Form renders in place, replacing the `<script>` tag's position in the DOM. | All tiers |
| `modal` | Clicking a trigger button opens a centered modal dialog. | Pro+ |
| `slide` | Clicking a trigger button slides a panel in from the right edge. | Pro+ |
| `bubble` | A floating action button in the corner opens a popover form. | Pro+ |

::callout{type="note" title="Hobby tier — inline only"}
Hobby accounts can publish embeds in `inline` mode. Upgrading to Pro or
above unlocks `modal`, `slide`, and `bubble` modes as well as brand
theming, custom success behavior, and lifecycle events.
::

## Trigger options

For `modal`, `slide`, and `bubble` modes the SDK can open the form in two ways:

- **Rendered trigger** (`trigger.kind = "rendered"`) — the SDK renders its own
  button with the label you configure.
- **Selector trigger** (`trigger.kind = "selector"`) — you supply a CSS
  selector; any matching element on the page (including elements added to the
  DOM after the script loads, via `MutationObserver`) opens the form when
  clicked. Use this to wire your own buttons or links.

```html
<!-- The SDK opens the form when any element with data-open-form="em_…" is clicked -->
<button data-open-form="em_AbCd1234567890EfGhIjKl">Request a quote</button>

<script async src="https://app.fastyoke.io/embed.js" data-embed="em_AbCd1234567890EfGhIjKl"></script>
```

The selector is configured inside the admin Embed tab, not in the HTML.

## `window.FastYoke` API

The script installs a `window.FastYoke` object that lets you programmatically
control embeds from your own JavaScript:

```js
// Open or close an embed by its embed_id
window.FastYoke.open('em_AbCd1234567890EfGhIjKl');
window.FastYoke.close('em_AbCd1234567890EfGhIjKl');

// List all embeds currently mounted on the page
console.log(window.FastYoke.embeds);
// → [{ embed_id: "em_…", mode: "modal", state: "closed" }]

// Subscribe to lifecycle events (see Events section)
const unsubscribe = window.FastYoke.on('fastyoke:success', (payload) => {
  console.log('Form submitted', payload);
});

// Stop listening
unsubscribe();
```

The `window.FastYoke` object is installed once even if multiple embed scripts
are on the same page.

## Configuring allowed origins

The FastYoke form iframe validates the embedding site's origin against a
per-tenant allowlist stored in **Settings → Embed domains**
(`/admin/settings#allowed-embed-domains`). The list is a space-separated set
of origins, for example:

```
https://www.example.com https://app.example.com
```

When the list is empty the server runs in wildcard mode (any origin may embed
your forms). When it contains at least one entry, a request from an origin not
on the list receives `403 Forbidden`.

::callout{type="warning" title="Configure origins before going live"}
If you save an embed but have not added your site's origin to the allowed
list, every visitor to your site will see the "Form temporarily unavailable"
placeholder. The Embed tab shows a warning banner with a direct link to the
settings page when no domains are configured.
::

The backend also injects a `Content-Security-Policy: frame-ancestors` header
that tells browsers which origins may frame the form page, providing a
defense-in-depth layer on top of the server-side origin check.

## Theming

::callout{type="note" title="Pro+ feature"}
Brand theming is stripped server-side for Hobby accounts. The form renders
with FastYoke's default theme regardless of what was saved.
::

The embed config carries three theme fields that map to CSS custom properties
applied to the form's outer wrapper:

| Config field | CSS variable | Default |
|---|---|---|
| `theme.primary_color` | `--fy-primary-color` | `#4f46e5` |
| `theme.border_radius` | `--fy-border-radius` | `8px` |
| `theme.font_family` | `--fy-font-family` | `inherit` |

These are configured in the **Brand theme** section of the Embed tab in the
admin shell. There is no way to override the theme from the page's HTML —
all theme configuration is server-side and tier-masked at render time.

## Lifecycle events

::callout{type="note" title="Pro+ feature"}
Lifecycle events (postMessage relay) are disabled server-side for Hobby
accounts. The `window.FastYoke.on()` calls below are no-ops on Hobby.
::

When **Emit postMessage events** is enabled in the embed config, the SDK
relays events from the form iframe to the host page as `CustomEvent`s on
`window`. Subscribe with `window.FastYoke.on()` or directly with
`window.addEventListener()`:

| Event name | Fires when |
|---|---|
| `fastyoke:view` | The form is displayed / scrolled into view |
| `fastyoke:start` | The visitor begins filling in the form |
| `fastyoke:submit` | The visitor submits (before server confirmation) |
| `fastyoke:success` | The server confirms a successful submission |
| `fastyoke:error` | A submission error is returned |
| `fastyoke:close` | The modal / slide / bubble chrome is dismissed |

Every event payload includes the `embed_id` of the originating embed plus any
additional fields from the iframe:

```js
window.addEventListener('fastyoke:success', (e) => {
  const { embed_id, ...rest } = e.detail;
  console.log('Submission accepted', embed_id, rest);
});
```

`fastyoke:close` always fires when the chrome closes, even when postMessage
events are disabled — it is a chrome event rather than an iframe-driven event.

You can choose which events are forwarded from the **Events** section of the
Embed tab. The server only relays events listed in `events_enabled`; events not
in the list are silently dropped before reaching the host page.

## Powered-by badge

Hobby accounts always display a "Powered by FastYoke" badge inside the embed.
The badge is rendered server-side (the `show_badge` field is set by the API,
not the client), so it cannot be removed by modifying the snippet. Pro and
above can opt out from **Branding** settings.

## Summary of tier differences

| Feature | Hobby | Pro+ |
|---|---|---|
| Inline mode | Yes | Yes |
| Modal / slide / bubble modes | No | Yes |
| Brand theming | No | Yes |
| Custom success behavior | No | Yes |
| Lifecycle events | No | Yes |
| Powered-by badge | Always on | Optional |
