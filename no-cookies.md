---
title: No cookies — FastYoke
summary: Zero cookies on the marketing site, one strictly-necessary session cookie on the app, no third-party trackers anywhere.
order: 90
---

::hero{eyebrow="Privacy" title="We don't use cookies." tagline="Zero on the marketing site. Exactly one strictly-necessary session cookie on the app, and nothing else, ever."}
::

::marketing-section{band="white" maxWidth="3xl"}
#default
<h2 class="text-2xl font-semibold text-[var(--brand-text-primary)]">The promise, exactly</h2>

<ul class="mt-6 space-y-4 text-sm text-[var(--brand-text-primary)]">
  <li><strong>www.fastyoke.io</strong> — zero cookies. Period.</li>
  <li><strong>app.fastyoke.io</strong> (signed in) — one cookie: <code>fy_session</code>, <code>Secure; HttpOnly; SameSite=Strict</code>. Holds your session JWT. Cleared on logout.</li>
  <li><strong>Third-party trackers, analytics pixels, ad networks, session replay</strong> — none, on either surface.</li>
</ul>
::

::marketing-section{band="gray" maxWidth="3xl"}
#default
<h2 class="text-2xl font-semibold text-[var(--brand-text-primary)]">Why this is the design, not the policy</h2>

<p class="mt-4 text-sm text-[var(--brand-text-primary)]">
  <strong>Single-tenant SQLite means there is no shared analytics warehouse.</strong>
  No place for cross-tenant behavioral data to land — by architecture,
  not promise.
</p>

<p class="mt-4 text-sm text-[var(--brand-text-primary)]">
  <strong>The marketing site is static MDC.</strong> Nothing to instrument.
  The FSM engine doesn't need to know who reads the pricing page.
</p>

<p class="mt-4 text-sm text-[var(--brand-text-primary)]">
  <strong>The app is a Rust monolith serving its own SPA.</strong> No
  third-party CDN or tag manager in the request path — one origin, one
  binary, one cookie.
</p>

<p class="mt-4 text-sm text-[var(--brand-text-primary)]">
  <strong>No retargeting, no funnel analytics, no surveillance pixels.</strong>
  If we want to know whether a feature is used, we ship the question to
  product — not a covert script.
</p>
::

::marketing-section{band="white" maxWidth="3xl"}
#default
<h2 class="text-2xl font-semibold text-[var(--brand-text-primary)]">What we use that isn't a cookie</h2>

<p class="mt-4 text-sm text-[var(--brand-text-primary)]">
  The marketing site writes three keys to your browser's
  <code>localStorage</code>. Each holds a UX preference you triggered;
  none leave your browser.
</p>

<ul class="mt-4 space-y-2 text-sm text-[var(--brand-text-primary)]">
  <li><code>fastyoke-announcement-dismissed-&lt;id&gt;</code> — remembers a marketing banner you dismissed</li>
  <li><code>fastyoke-docs-recent-searches</code> — your last few /docs searches (your browser only)</li>
  <li><code>fastyoke-docs-color-scheme</code> — dark / light / system preference</li>
</ul>

<p class="mt-4 text-sm text-[var(--brand-text-primary)]">
  All three are exempt from consent requirements under GDPR recital 32
  and ePrivacy Art 5(3) — they hold UX preferences you triggered and
  never leave your browser.
</p>
::

::marketing-section{band="gray" maxWidth="3xl"}
#default
<h2 class="text-2xl font-semibold text-[var(--brand-text-primary)]">Verify it yourself</h2>

<ol class="mt-4 space-y-3 text-sm text-[var(--brand-text-primary)]">
  <li>Open DevTools → Application → Cookies → <code>https://www.fastyoke.io</code> — empty.</li>
  <li>Sign in, open <code>https://app.fastyoke.io</code> → Cookies — exactly one entry, <code>fy_session</code>.</li>
</ol>
::
