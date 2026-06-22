---
title: Lab M14 — Ledger & E-sign
summary: Author a Revenue CoA account and a Posting Rule via REST, post a journal entry, reverse it, then upload a PDF envelope and verify the Certificate of Completion out-of-band.
order: 28
---

# Lab M14 — Ledger & E-sign

This lab pairs with **Module M14 — Yoke Ledger + E-signatures**
in the [syllabus](/docs/training/syllabus).

## What you'll do

Author a Revenue Chart-of-Accounts entry and a Posting Rule
that books invoices to it. Create a journal entry, post it,
then reverse it. Then switch to the e-sign side: upload a
PDF envelope, route it to a signer, sign as that signer,
and verify the sealed PDF's Certificate of Completion via
the platform's public verify endpoint.

## Before you begin

- A FastYoke tenant with the Yoke Ledger app installed and
  entitled (covered in M10).
- A `fy_pat_` PAT with `gl:write` and `esign:admin` scopes
  (or admin session JWT).
- A small PDF to use as an envelope (a one-page invoice
  works).
- `curl` and `jq`.
- About 50 minutes.

## Or use Postman

If you prefer a GUI, run the GL portion (Steps 1–4) from
Postman instead of `curl`. The e-sign Steps 5–7 are
GUI / out-of-band — they stay on this lab page.

**Import once:** in Postman, choose **File → Import → Link**
and paste this URL:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

Click **Import**. Open the **Week 7 — Specialized Yokes → M14 — Ledger & E-sign** folder, set the `FY_TOKEN` environment variable, then run each request in order. Step 4 (Post entry) references the `FY_GL_ENTRY` captured by Step 3.

The `curl` steps below remain the source of truth.

## Steps

1. **Author a Revenue CoA account.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"code":"4000","name":"Service Revenue","type":"revenue"}' \
     https://www.fastyoke.io/api/v1/tenant/gl/accounts
   ```

   **Checkpoint:** 200 with the new account id.
2. **Author a Posting Rule.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"rule_key":"invoice_posted","entries":[{"account":"4000","side":"credit","amount_path":"$.total"}]}' \
     https://www.fastyoke.io/api/v1/tenant/gl/posting-rules
   ```

   **Checkpoint:** 200. The rule will fire whenever an
   `invoice_posted` event lands and credit `4000` with the
   invoice total.
3. **Create a draft journal entry.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"memo":"M14 lab — sample invoice","lines":[{"account":"4000","side":"credit","amount_cents":10000}]}' \
     https://www.fastyoke.io/api/v1/tenant/gl/journal-entries
   ```

   **Checkpoint:** 200 with the entry id and `state:
   "draft"`.
4. **Post the entry.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/gl/journal-entries/$ID/post
   ```

   **Checkpoint:** 200 with `state: "posted"`. The entry is
   now **immutable** — no further `PATCH` will be accepted.
5. **Reverse the entry.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/gl/journal-entries/$ID/reverse
   ```

   **Checkpoint:** 200 with a new reversal entry id; the
   original stays `posted` and a new entry with the
   mirrored sides is appended. History is never edited.
6. **Upload a PDF envelope (GUI).** **Admin → E-Sign → New
   envelope.** Upload your one-page invoice. Add a signer
   (your own email is fine for the lab). Click **Send**.
7. **Sign as the signer.** Open the signer email; click
   the link; sign in the portal.

   **Checkpoint:** the envelope status flips to
   `completed`. The platform serves a sealed PDF with the
   **Certificate of Completion** as the last page.
8. **Verify the sealed PDF.** The Certificate of Completion
   carries a verification URL of the form
   `https://www.fastyoke.io/api/v1/public/esign/verify?hash=…`.
   Hit it.

   **Checkpoint:** 200 with the hash, signer chain, and
   UTC timestamps — no auth required.

## What you'll have at the end

A Revenue account, a Posting Rule, a posted journal entry,
a reversal entry, a sealed PDF, and a public-verify response
proving the seal.

## Stuck?

See [Lab M14 — Solution](/docs/training/labs/M14-ledger-esign-solution).

## Next

You've completed Week 7. Lab M15 ships in the Week 8 lab
pack PR. Return to the [syllabus](/docs/training/syllabus)
for the Week 8 reading list.
