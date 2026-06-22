---
title: Lab M4 — Public-link form
summary: Compose a 4-field public form, publish a tokenized public link, submit anonymously, observe the FSM job the platform creates at submit time, then trigger anti-abuse and observe the 429 + Retry-After response.
order: 8
---

# Lab M4 — Public-link form

This lab pairs with **Module M4 — Forms** in the
[syllabus](/docs/training/syllabus).

## What you'll do

Compose a 4-field public form (a text field, a
single-select, a file upload, and a checkbox). Wire
validation against a zod-mirrored schema. Publish a
**public link**. Submit the form anonymously from a fresh
browser session or `curl`. Observe the resulting FSM job
created at submit time. Then trigger anti-abuse — submit
too fast — and observe the rate-limit response.

## Before you begin

- A FastYoke tenant you're an admin of.
- An admin session (forms are authored in the admin
  shell, not via API token).
- `curl` to exercise the public link and the anti-abuse
  trigger.
- About 45 minutes.

## Or use Postman

If you prefer a GUI, run the lab steps from Postman instead
of `curl`. Each request has a **Tests** tab with the same
checkpoint assertions the `curl` lab pins.

**Import once:** in Postman, choose **File → Import → Link**
and paste this URL:

```
https://www.fastyoke.io/training/postman/fastyoke-training.postman_collection.json
```

Click **Import**. Open the **Week 2 — Core primitives I → M4
— Public-link form** folder, set the `FY_TOKEN` and the
public-link token (replace `REPLACE_WITH_TOKEN` in the
request URLs) before sending. Step 1 (composing the form)
still happens in the admin shell — Postman can't author
forms — but submission and inspection both work from the
collection.

The `curl` steps below remain the source of truth.

## Steps

1. **Compose the form** in the admin shell at
   **Settings → Forms → New form**. Add four fields:
   - A required text field (e.g., `name`).
   - A single-select with two or three options
     (e.g., `urgency`: low / medium / high).
   - A file-upload field (allow common image types).
   - A required checkbox (e.g., `consent`).

   **Checkpoint:** the form preview in the admin shell
   renders all four fields. The file-upload accepts a
   sample image.
2. **Publish the public link.** Use the **Publish**
   action to mint a tokenized public URL of the shape
   `https://www.fastyoke.io/myform/<token>`.

   **Checkpoint:** opening the URL in a fresh incognito
   window renders the form without an auth prompt.
3. **Submit the form anonymously.** Either fill it in
   the browser, or `curl` against
   `/api/v1/public/forms/<token>/submit` with a
   multipart body (see the solution page for the exact
   shape).

   **Checkpoint:** the submission returns `200` with a
   `submission_id`. The form's submission view in the
   admin shell shows the new row.
4. **Inspect the FSM job created at submit time.** Many
   forms wire a workflow trigger; if your form does,
   the platform creates a job at submission.

   **Checkpoint:** `GET /api/v1/tenant/jobs` (with an
   admin session JWT — the test-token route works for
   convenience) lists a new job whose source names
   your form submission.
5. **Trigger anti-abuse.** From a shell, run a
   10-iteration loop hitting the public submit endpoint
   in under 30 seconds.

   **Checkpoint:** at some point in the burst, the
   submit response returns `429` with a `Retry-After`
   header naming a seconds-to-wait value. The platform
   rate-limited the public link.

## What you'll have at the end

A live public-link form URL, a verified submission, a
created FSM job, and a captured `429` response
demonstrating the anti-abuse trigger.

## Stuck?

See [Lab M4 — Solution](/docs/training/labs/M4-forms-solution).

## Next

You've completed Week 2. Lab M5 ships in the Week 3 lab
pack PR. Return to the
[syllabus](/docs/training/syllabus) for the Week 3
reading list.
