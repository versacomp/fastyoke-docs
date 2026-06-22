---
title: Lab M11 — Webhooks
summary: Subscribe to job.transition, verify HMAC end-to-end on a receiver, force failures into the DLQ, then replay deliveries and observe dedup by delivery id.
order: 22
---

# Lab M11 — Webhooks

This lab pairs with **Module M11 — Webhooks + Notifications +
Messaging** in the [syllabus](/docs/training/syllabus).

## What you'll do

Subscribe to `job.transition` via `POST /webhooks`. Stand up a
small HMAC-verifying receiver. Fire a transition on a job
(reuse the `service_ticket` from M5/M6). Verify the HMAC on
the receiver. Force a 500 to push deliveries into the DLQ.
Hit `POST /webhooks/:id/replay-dlq` and observe that the
platform redelivers with the same `X-FastYoke-Delivery` id —
so your receiver can deduplicate cleanly.

## Before you begin

- A FastYoke tenant you're an admin of.
- A `fy_pat_` PAT with `webhooks:admin` scope (or an admin
  session JWT).
- `curl` and `jq`.
- A way to receive HTTPS — RequestBin, ngrok + a tiny Node
  server, or `webhook.site`.
- A job to drive — the `service_ticket` FSM from M5/M6 works,
  or any FSM with at least one transition.
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

Click **Import**. Open the **Week 6 — Events, messaging, data ops → M11 — Webhooks** folder, set the `FY_TOKEN` and
`FY_ID` environment variables, then run each request in order.
Step 5's Tests tab asserts the replay-dlq response — that
confirms the platform redrove the failed deliveries.

The `curl` steps below remain the source of truth.

## Steps

1. **Subscribe to `job.transition`.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"url":"https://your-receiver.example","events":["job.transition"],"description":"M11 lab"}' \
     https://www.fastyoke.io/api/v1/tenant/webhooks
   ```

   **Checkpoint:** 200 with an `id` (the subscription id)
   and a one-time-shown `signing_secret` — copy that into
   `FY_SECRET` for HMAC verification.
2. **Fire a transition on a job.**

   ```bash
   curl -i -X POST \
     -H "Authorization: Bearer $FY_PAT" \
     -H "Content-Type: application/json" \
     -d '{"event":"submit_review"}' \
     https://www.fastyoke.io/api/v1/tenant/jobs/$JOB_ID/transition
   ```

   **Checkpoint:** the receiver sees an HTTPS POST with an
   `X-FastYoke-Signature` header and an `X-FastYoke-Delivery`
   header. The JSON body's top-level `event` is
   `job.transition`.
3. **Verify the HMAC on the receiver.** Compute
   `HMAC-SHA256(FY_SECRET, raw_body)` and compare to the
   `sha256=…` value in `X-FastYoke-Signature`.

   **Checkpoint:** the constant-time comparison matches.
4. **Force a 500 into the DLQ.** Have your receiver return
   `HTTP 500` for the next two transitions. Fire two more
   transitions.

   **Checkpoint:** `GET /webhooks/$SUB_ID/deliveries` shows
   the two failed deliveries with attempt counters above 1.
5. **Replay the DLQ.**

   ```bash
   curl -i -X POST -H "Authorization: Bearer $FY_PAT" \
     https://www.fastyoke.io/api/v1/tenant/webhooks/$SUB_ID/replay-dlq
   ```

   **Checkpoint:** the receiver gets the two replayed
   deliveries with the same `X-FastYoke-Delivery` ids they
   carried originally — your receiver should treat the
   second arrival as a no-op (dedup by delivery id).

## What you'll have at the end

A working receiver that verifies HMAC, deduplicates by
`X-FastYoke-Delivery`, and survived a DLQ replay.

## Stuck?

See [Lab M11 — Solution](/docs/training/labs/M11-webhooks-solution).

## Next

[Lab M12 — Bulk ops & Reports](/docs/training/labs/M12-bulk-ops-reports).
