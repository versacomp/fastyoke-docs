---
title: Quiz M9 — Extensions
summary: 10 multiple-choice questions on signed WASM bundles, the manifest scope vocabulary, WIT host imports, and fuel/memory caps.
order: 10
---

# Quiz M9 — Extensions

Pairs with [Lecture M9](/docs/training/lectures/M9-extensions)
and [Lab M9](/docs/training/labs/M9-extensions).

**Q1.** Why must extensions be signed?

a) For licensing
b) To prevent loading unauthorized code
c) For performance
d) Backwards compat

<details>
<summary>Answer + why</summary>

**b)** The platform refuses unsigned bundles — the
authoring key is tenant-scoped.

</details>

**Q2.** What's the scope vocabulary an extension manifest draws from?

a) A separate catalog
b) The same 72-permission catalog as PATs
c) Free-form strings
d) Inherited from the installing user role

<details>
<summary>Answer + why</summary>

**b)** Unified vocabulary — granting a scope means the
same thing across PATs and extensions.

</details>

**Q3.** How does an extension talk to the host?

a) Direct file I/O
b) WIT host imports — storage reads, entity reads/writes, SSRF-guarded HTTP
c) Shared memory
d) gRPC over a unix socket

<details>
<summary>Answer + why</summary>

**b)** The WIT imports are the only door out of the
sandbox.

</details>

**Q4.** What stops a runaway extension call?

a) Wall-clock timeout
b) Fuel budget + memory ceiling per invocation
c) The OS oom-killer
d) Nothing — caveat emptor

<details>
<summary>Answer + why</summary>

**b)** Fuel + memory caps. Exceeding either kills the call
cleanly.

</details>

**Q5.** Can an extension perform arbitrary file I/O?

a) Yes — wasmtime supports it
b) No — there's no host import for raw file I/O
c) Only via the entity payload
d) Only on Enterprise

<details>
<summary>Answer + why</summary>

**b)** Persistence happens via entity reads/writes, not
files.

</details>

**Q6.** Which language is the smoothest for authoring extensions?

a) Python
b) Rust (the smallest + fastest path)
c) Go
d) JavaScript

<details>
<summary>Answer + why</summary>

**b)** JS is supported via QuickJS-on-WASM but Rust is
smaller, faster, and easier to debug.

</details>

**Q7.** What's the cloud sandbox for?

a) Production runtime
b) The authoritative dev environment
c) A throwaway preview
d) Documentation only

<details>
<summary>Answer + why</summary>

**b)** Never assume local behavior generalizes — the
cloud sandbox is the truth.

</details>

**Q8.** What happens at install time if the manifest asks for more scopes than the installing admin can grant?

a) Install succeeds with reduced scopes
b) Install fails — the admin can't grant more than they hold
c) Platform admin is notified
d) The extension gets all requested scopes regardless

<details>
<summary>Answer + why</summary>

**b)** No privilege escalation through extensions.

</details>

**Q9.** Can extensions skip signing during local dev?

a) Yes — the platform allows unsigned in dev
b) No — the CLI signs with your dev key automatically
c) Only by setting an env var
d) Only with admin override

<details>
<summary>Answer + why</summary>

**b)** Always signed; the dev key just gets used
automatically.

</details>

**Q10.** What's the SSRF guard's role for extension HTTP?

a) Caches responses
b) Refuses non-HTTPS, private ranges, link-local, IPv6 ULA
c) Rate-limits
d) Adds auth headers

<details>
<summary>Answer + why</summary>

**b)** Same guard as outbound webhooks. Refusals are a
separate counter, not toward auto-pause.

</details>
