---
title: 101 — Your first API integration
summary: In ten minutes, mint a one-hour test token, POST an entity, fire a workflow transition against it, and receive the matching outbound webhook. The proof at the end is a single HTTPS request you can replay.
order: 2
---

# 101 — Your first API integration

## What you'll build

In about ten minutes you'll end with a temporary
HTTPS endpoint (RequestBin or equivalent) holding a
real `job.transition` webhook delivery from your
tenant. The body is what every later integration will
receive.

Final proof — a single POST you can replay:

```http
POST https://your-bin.requestbin.example HTTP/1.1
Content-Type: application/json
X-FastYoke-Event: job.transition
X-FastYoke-Delivery: wh_evt_01HXZ…
X-FastYoke-Timestamp: 1750905600
X-FastYoke-Signature: sha256=…

{
  "id": "wh_evt_01HXZ…",
  "event": "job.transition",
  "occurred_at": "2026-06-21T12:00:00Z",
  "tenant_id": "tnt_01HW…",
  "data": {
    "job_id": "job_…",
    "from_state": "Draft",
    "to_state": "Active",
    "event_name": "activate"
  }
}
```

## Before you begin

- A FastYoke tenant you're an admin of. The free tier
  is fine.
- `curl` on your shell.
- A way to receive an HTTPS request. This tutorial
  uses [RequestBin](https://requestbin.com); ngrok and
  `webhook.site` work identically.

## Steps

### 1. Mint a one-hour test token

Open the admin shell at **Settings → Tokens → Generate test token**.
See the [quick test token reference](/docs/auth/api-tokens#quick-test-token)
for the full explanation of what gets minted.

Copy the token. You now have a bearer that mirrors
your admin session for one hour.

**Checkpoint** — list the tenant's PATs (the list may
be empty; the 200 status is the win):

```bash
curl -i \
  -H "Authorization: Bearer $FY_TOKEN" \
  https://www.fastyoke.io/api/v1/tenant/api-tokens
```

Expected: `HTTP/2 200` and a JSON array (possibly
empty). Anything other than 200 means re-mint.

### 2. Find a schema with a transition

Your tenant has at least one FSM schema installed by
default. `GET /api/v1/tenant/schemas` returns them.

```bash
curl -s -H "Authorization: Bearer $FY_TOKEN" \
  https://www.fastyoke.io/api/v1/tenant/schemas \
  | jq '.[0] | {name, entity, transitions: [.transitions[] | {event, from, to}]}'
```

**Checkpoint** — at least one transition is listed. If
the array is empty, install a Yoke from the
[Marketplace](/docs/marketplace) (Yoke Ledger is free)
and re-run.

Save the `entity` name and one `event` for the next
steps.

### 3. Create an entity record

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"payload":{"display_name":"My first record"}}' \
  https://www.fastyoke.io/api/v1/tenant/entities/<entity>
```

**Checkpoint** — response carries an `id`. Save it
into `$FY_ID`. `GET .../<id>` returns the same body —
that round-trip lets you verify the write landed.

### 4. Wire a webhook subscription

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-bin.requestbin.example",
    "events": ["job.transition"],
    "description": "101 tutorial"
  }' \
  https://www.fastyoke.io/api/v1/tenant/webhooks
```

Save the response `id` into `$FY_SUB`.

**Checkpoint** — fire a synthetic delivery and confirm
it lands on your bin:

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FY_TOKEN" \
  https://www.fastyoke.io/api/v1/tenant/webhooks/$FY_SUB/test
```

Within a few seconds, your RequestBin shows a POST
with `X-FastYoke-Event` set.

### 5. Trigger a transition

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"event":"<your-event-name>"}' \
  https://www.fastyoke.io/api/v1/tenant/jobs/$FY_ID/transition
```

**Checkpoint** — within seconds, the bin shows a POST
with `X-FastYoke-Event: job.transition` and a body
whose `data.event_name` matches your event.

## What you built

- One new entity row in your tenant.
- One `job.transition` row in `event_log`.
- One webhook subscription rendered in
  **Settings → Webhooks**.
- One receipt at your RequestBin, ready to replay.

## Next

[201 — Sync data into FastYoke](/docs/tutorials/201-csv-importer) —
production patterns wrapped around a CSV importer.
