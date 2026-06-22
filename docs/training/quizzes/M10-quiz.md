---
title: Quiz M10 — Marketplace
summary: 10 multiple-choice questions on the install/uninstall loop, the retention contract, Tenant Yokes vs Marketplace listings, and clone semantics.
order: 11
---

# Quiz M10 — Marketplace

Pairs with [Lecture M10](/docs/training/lectures/M10-marketplace)
and [Lab M10](/docs/training/labs/M10-marketplace).

**Q1.** What does uninstalling a Marketplace listing do?

a) Deletes the listing and all seeded data
b) Deletes the listing; preserves seeded data (retention contract)
c) Marks it as inactive but keeps everything
d) Returns the tenant to factory state

<details>
<summary>Answer + why</summary>

**b)** Data outlives the listing. Misclicks don't lose
your books.

</details>

**Q2.** Updating a Marketplace listing does what to existing Tenant Yoke clones?

a) Auto-migrates them
b) Nothing — clones are independent
c) Prompts the admin to opt in
d) Marks them as outdated

<details>
<summary>Answer + why</summary>

**b)** A clone is yours. Upstream updates don't reach it.

</details>

**Q3.** Marketplace gating is enforced via…

a) Route-level scope checks
b) `marketplace_entitlements` rows checked at install/runtime
c) Customer billing tier alone
d) Honor system

<details>
<summary>Answer + why</summary>

**b)** Entitlement rows gate whether routes are reachable.

</details>

**Q4.** What four things does a Marketplace install include?

a) Just apps
b) Apps + UI pages + FSM schemas + optionally seeded rows
c) Apps + a download script
d) Apps + extensions

<details>
<summary>Answer + why</summary>

**b)** Seeded rows make the install useful from minute
one.

</details>

**Q5.** A Tenant Yoke is…

a) A copy of a Marketplace listing the tenant can customize
b) An install instance of a Marketplace listing (no customization)
c) A subscription tier
d) A backup of a Marketplace listing

<details>
<summary>Answer + why</summary>

**a)** The customization path. Authoring a Tenant Yoke is
the on-ramp to publishing your own listing.

</details>

**Q6.** Owned-schemas separation means…

a) Schemas are immutable once cloned
b) A clone copies the schemas; the originals remain owned by the source
c) Cloning is read-only
d) Only Enterprise can clone

<details>
<summary>Answer + why</summary>

**b)** Source updates don't migrate clones — by design.

</details>

**Q7.** What's the right way to truly purge data an app seeded?

a) Uninstall
b) Delete the rows explicitly first, then uninstall
c) Drop the tenant DB
d) Open a support ticket

<details>
<summary>Answer + why</summary>

**b)** Retention is deliberate. Explicit deletes for
explicit purges.

</details>

**Q8.** What's a deterministic seed prefix used for?

a) Random salt
b) Ensuring the same install seeds the same rows across tenants without primary-key collisions
c) Anti-spam
d) Bandwidth optimization

<details>
<summary>Answer + why</summary>

**b)** Per-tenant + per-listing prefix makes seeds
idempotent and collision-free.

</details>

**Q9.** Forgetting the entitlement check in extension code does what?

a) Returns 401
b) Ships a paid feature for free
c) Refuses the request
d) Logs a warning

<details>
<summary>Answer + why</summary>

**b)** Exactly the failure mode the gating exists to
prevent.

</details>

**Q10.** A "factory reset" via uninstall…

a) Works as expected
b) Does NOT work — that's not what uninstall does
c) Requires admin override
d) Costs $100

<details>
<summary>Answer + why</summary>

**b)** Uninstall removes the listing only. The retention
contract is deliberate.

</details>
