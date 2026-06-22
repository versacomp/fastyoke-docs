---
title: Lab M2 — Token walkthrough
summary: Mint a one-hour test token and a long-lived fy_pat_ token. Call the tenant API with each. Observe two hard-refusal cases. Capture the three working request shapes in a shell script.
order: 4
---

# Lab M2 — Token walkthrough

This lab pairs with **Module M2 — Auth & token layers** in
the [syllabus](/docs/training/syllabus).

## What you'll do

Mint a one-hour test token and a long-lived `fy_pat_` token
in your tenant. Call the tenant API with each. Then attempt
the "hard refusal" cases (mint a new PAT from a PAT; mint a
test token from a PAT). End with a shell script capturing
the three working request shapes plus the two refusal
observations as comments.

## Before you begin

- A FastYoke tenant you're an admin of.
- `curl` on your shell.
- About 20 minutes.

## Or use Postman

If you prefer a GUI, run the lab steps from Postman instead
of `curl`. Each request has a **Tests** tab with the same
checkpoint assertions the `curl` lab pins.

**Import once:** in Postman, choose **File → Import → Link**
and paste this URL:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

Click **Import**. Open the **Week 1 — Foundations → M2 —
Token walkthrough** folder, set the `FY_TOKEN` environment
variable, then run each request in order. Green checks in
the Tests tab mean the step worked.

The `curl` steps below remain the source of truth.

## Steps

1. **Mint a one-hour test token.** Open Settings →
   Tokens → Generate test token. Copy the value into
   `FY_TEST_TOKEN`.
   **Checkpoint:** `curl -i -H "Authorization: Bearer
   $FY_TEST_TOKEN" https://www.fastyoke.io/api/v1/tenant/api-tokens`
   returns `HTTP/2 200` with a JSON list (may be empty).
2. **Mint a `fy_pat_` token with `data:read` and
   `data:write` scopes** from Settings → Tokens → New token.
   Copy into `FY_PAT`.
   **Checkpoint:** repeat the previous `curl` with
   `$FY_PAT` — returns 200 with the same list.
3. **Compare the two responses.** Both worked. But the
   underlying authority is different.
   **Checkpoint:** state which token mirrors a session and
   which is an opaque scope-gated credential. (Answer:
   the test token mirrors session; `fy_pat_` is
   scope-gated.)
4. **Attempt to mint a NEW `fy_pat_` from the existing
   PAT.**
   `curl -i -X POST -H "Authorization: Bearer $FY_PAT" -H
   "Content-Type: application/json" -d
   '{"name":"nope","scopes":["data:read"]}'
   https://www.fastyoke.io/api/v1/tenant/api-tokens`.
   **Checkpoint:** `HTTP/2 403` — one of the four
   hard-refusal codes from
   [/docs/auth/api-tokens#hard-refusals](/docs/auth/api-tokens#hard-refusals).
5. **Attempt to mint a test token from the PAT.**
   `curl -i -X POST -H "Authorization: Bearer $FY_PAT"
   https://www.fastyoke.io/api/v1/tenant/api-tokens/test-token`.
   **Checkpoint:** `HTTP/2 403` with
   `delegated_credential_refused` in the body.
6. **Capture working shapes in a script.** Write a
   three-section `auth-walkthrough.sh` containing the
   three working request shapes (test-token list, PAT
   list, two refusal observations as comments).
   **Checkpoint:** running the script end-to-end prints
   the three results in order.

## What you'll have at the end

An `auth-walkthrough.sh` script you can re-run to
demonstrate the three authentication paths and the two
hard refusals.

## Stuck?

See [Lab M2 — Solution](/docs/training/labs/M2-token-walkthrough-solution).

## Next

[Lab M3 — Author a vehicle entity](/docs/training/labs/M3-entities).
