---
title: Lab M2 — Solution
summary: Reference solution for Lab M2 — exact curl commands, expected response shapes, and the full auth-walkthrough.sh script.
order: 5
---

# Lab M2 — Solution

::callout{type="warning" title="Stop"}
This page is the **answer key** for
[Lab M2](/docs/training/labs/M2-token-walkthrough). Work the
lab first — the friction of clicking through is intentional.
::

## Answer per step

### Step 1 — Test-token list

```bash
curl -i \
  -H "Authorization: Bearer $FY_TEST_TOKEN" \
  https://www.fastyoke.io/api/v1/tenant/api-tokens
```

Expected:

```
HTTP/2 200
content-type: application/json

[]
```

(or a populated list if your tenant has previously-minted
`fy_pat_` tokens).

### Step 2 — PAT list

Same request shape with `$FY_PAT` in place of `$FY_TEST_TOKEN`.
Expected: `HTTP/2 200` and the same body.

### Step 3 — Session vs. scope-gated

The test token mirrors the admin session — it carries the
same authority your interactive browser session does, time-
boxed to one hour. The four "hard refusals" that bind
`fy_pat_` tokens do NOT apply to test tokens.

The `fy_pat_` token is scope-gated. It carries the explicit
scope set you picked at mint time. It is subject to the
four hard refusals (cannot install / uninstall extensions,
cannot mint or revoke other PATs).

### Step 4 — Refused PAT mint from a PAT

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  -H "Content-Type: application/json" \
  -d '{"name":"nope","scopes":["data:read"]}' \
  https://www.fastyoke.io/api/v1/tenant/api-tokens
```

Expected:

```
HTTP/2 403
content-type: application/json

{"error":"cannot_mint_pat_from_pat"}
```

(Error code may vary slightly across versions; the 403 + a
refusal code from the hard-refusal set is the contract.)

### Step 5 — Refused test-token mint from a PAT

```bash
curl -i -X POST \
  -H "Authorization: Bearer $FY_PAT" \
  https://www.fastyoke.io/api/v1/tenant/api-tokens/test-token
```

Expected:

```
HTTP/2 403
content-type: application/json

{"error":"delegated_credential_refused"}
```

This is the anti-delegation refusal — a non-human session
(another PAT, or even a test token) cannot mint a fresh
test token. The minted JWT always traces back to a human
admin sign-in.

### Step 6 — A sample `auth-walkthrough.sh`

```bash
#!/usr/bin/env bash
set -e

# 1) Test-token list — short-lived session-mirroring JWT
curl -fsS -H "Authorization: Bearer $FY_TEST_TOKEN" \
  https://www.fastyoke.io/api/v1/tenant/api-tokens

# 2) PAT list — long-lived scope-gated bearer
curl -fsS -H "Authorization: Bearer $FY_PAT" \
  https://www.fastyoke.io/api/v1/tenant/api-tokens

# 3) Refusals (these run with `|| true` because they exit non-zero):
# - Minting a PAT from a PAT returns 403 hard-refusal.
# - Minting a test token from a PAT returns 403 delegated_credential_refused.
```

If yours looks meaningfully like this, you've done the lab.

## Back to the lab

[Lab M2 — Token walkthrough](/docs/training/labs/M2-token-walkthrough).
