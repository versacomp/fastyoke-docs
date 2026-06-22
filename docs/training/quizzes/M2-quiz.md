---
title: Quiz M2 — Auth & token layers
summary: 10 multiple-choice questions on session JWTs, fy_pat_ PATs, the one-hour test token, scopes vs roles, and the anti-delegation refusal.
order: 3
---

# Quiz M2 — Auth & token layers

Pairs with [Lecture M2](/docs/training/lectures/M2-auth)
and [Lab M2](/docs/training/labs/M2-token-walkthrough).

**Q1.** What's the prefix for personal access tokens?

a) `fy_jwt_`
b) `fy_pat_`
c) `pat_fy_`
d) `tok_`

<details>
<summary>Answer + why</summary>

**b)** Personal Access Token, prefixed `fy_pat_` for
easy grepping in logs and secret scanners.

</details>

**Q2.** What scope vocabulary do extensions and PATs share?

a) Each has its own catalog
b) Both draw from the same 72-permission catalog
c) Extensions are scopeless; PATs aren't
d) Both inherit from the user role

<details>
<summary>Answer + why</summary>

**b)** Authoring once means granting `data:write` to a PAT
means exactly the same thing as granting it to an
extension.

</details>

**Q3.** What does the WorkOS SSO callback resolve a user's tenant by?

a) Email domain matching
b) A claim in the OIDC token
c) A workspace assignment configured in WorkOS
d) The Referer header

<details>
<summary>Answer + why</summary>

**c)** Tenant is determined by the WorkOS workspace
assignment, not the email.

</details>

**Q4.** Why is the one-hour test JWT *not* subject to PAT hard refusals?

a) Bug — it should be
b) It mirrors the caller's authority instead of delegating
c) It's shorter-lived
d) It's a different signing key

<details>
<summary>Answer + why</summary>

**b)** A test JWT acts as the issuing admin themselves, so
"refuse to delegate" doesn't apply.

</details>

**Q5.** What does `delegated_credential_refused` mean?

a) Token expired
b) PAT cannot impersonate other users
c) WorkOS callback failed
d) Wrong scope

<details>
<summary>Answer + why</summary>

**b)** PATs act as the issuing user; trying to act as
someone else returns this refusal rather than silently
acting.

</details>

**Q6.** Role vs scope — which is which?

a) Role gates the API; scope gates the UI
b) Role gates the UI; scope gates the API
c) They're aliases
d) Only role is enforced

<details>
<summary>Answer + why</summary>

**b)** UI surfaces gate by role; API endpoints gate by
scope. A PAT with `data:read` can't call a `data:write`
endpoint even if the underlying user is admin.

</details>

**Q7.** Why are PATs not stored hashed if they're shown only once?

a) They are stored hashed
b) Backwards compatibility
c) For revocation lookups
d) SQLite can't hash

<details>
<summary>Answer + why</summary>

**a)** They are. The token surface returns the cleartext
once at creation; storage is a hash.

</details>

**Q8.** When does the session JWT expire?

a) Never
b) After a short window — re-issued on activity
c) After 24h fixed
d) On logout only

<details>
<summary>Answer + why</summary>

**b)** Sessions are short-lived JWTs refreshed on activity.
This bounds the blast radius if a session cookie leaks.

</details>

**Q9.** Which is **not** a token shape FastYoke ships?

a) Session JWT
b) `fy_pat_` PAT
c) WorkOS AuthKit session
d) Long-lived password cookie

<details>
<summary>Answer + why</summary>

**d)** Cookies hold the session JWT, not a password. The
platform never ships a "remember me" password token.

</details>

**Q10.** What's the right response if a PAT leaks to source control?

a) Rotate it immediately, then audit usage logs
b) Wait for it to expire
c) Add a `.gitignore` entry
d) Reduce its scope

<details>
<summary>Answer + why</summary>

**a)** PATs are long-lived; one leaked PAT can drain a
billing wallet. Rotate first, investigate second.

</details>
