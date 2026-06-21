---
title: 201 — Sync data into FastYoke
summary: Build a 60-line Node script that imports a CSV of customers into your tenant's entity API. Idempotency keys keep re-runs safe, Retry-After respects rate limits, and a checkpoint file recovers from mid-batch failure.
order: 3
---

# 201 — Sync data into FastYoke

## What you'll build

A small Node script that reads
`customers.csv`, POSTs each row to your tenant's
entity API with a stable per-row idempotency key, and
is safe to:

- **Run twice** — duplicates collapse into one record.
- **Be rate-limited mid-batch** — backoff respects
  `Retry-After`.
- **Be killed and restarted** — a `.checkpoint` file
  resumes from the last successful row.

## Before you begin

- An `fy_pat_` PAT minted with `data:write` scope. The
  one-hour test token isn't right here — this is a
  non-interactive script. See
  [API tokens](/docs/auth/api-tokens) for the mint
  flow.
- Node 20+.
- A CSV file. Sample:

```csv
email,display_name
alice@example.com,Alice
bob@example.com,Bob
carol@example.com,Carol
```

## Steps

### 1. Script skeleton

`importer.mjs`:

```js
import fs from 'node:fs';
import { createHash } from 'node:crypto';

const TOKEN = process.env.FY_TOKEN;
const BASE = 'https://www.fastyoke.io';
const ENTITY = 'customer';
const CSV = './customers.csv';

const rows = fs.readFileSync(CSV, 'utf8')
  .trim().split('\n').slice(1)
  .map(line => {
    const [email, display_name] = line.split(',');
    return { email, display_name };
  });

for (const row of rows) {
  console.log('POST', row.email);
  // TODO: POST + checkpoint
}
```

**Checkpoint** — `node importer.mjs` prints one line
per CSV row.

### 2. Idempotency key per row

The `Idempotency-Key` header makes a `POST` safe to
retry. Re-running the same request with the same key
returns the original response without creating a
second record. See
[Idempotency](/docs/developers/idempotency) for the
contract.

Compute a stable key per row — `sha256(email)`
truncated is plenty:

```js
function key(row) {
  return 'csv-' + createHash('sha256')
    .update(row.email).digest('hex').slice(0, 32);
}

async function postRow(row) {
  const res = await fetch(
    `${BASE}/api/v1/tenant/entities/${ENTITY}`,
    {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${TOKEN}`,
        'Content-Type': 'application/json',
        'Idempotency-Key': key(row),
      },
      body: JSON.stringify({ payload: row }),
    },
  );
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}

for (const row of rows) {
  const out = await postRow(row);
  console.log('OK', row.email, out.id);
}
```

**Checkpoint** — run twice; the second run prints the
same `id` per row. No duplicate records appear in your
tenant.

### 3. Backoff on `Retry-After`

A burst of POSTs may hit the per-tenant rate limit and
return `429`. The platform sends a `Retry-After`
header naming the seconds to wait. See
[Rate limits](/docs/developers/rate-limits).

Wrap `postRow` in a retry loop:

```js
async function postWithRetry(row, attempts = 5) {
  for (let i = 0; i < attempts; i++) {
    const res = await fetch(
      `${BASE}/api/v1/tenant/entities/${ENTITY}`,
      {
        method: 'POST',
        headers: {
          Authorization: `Bearer ${TOKEN}`,
          'Content-Type': 'application/json',
          'Idempotency-Key': key(row),
        },
        body: JSON.stringify({ payload: row }),
      },
    );
    if (res.ok) return res.json();
    if (res.status === 429) {
      const wait = +(res.headers.get('Retry-After') ?? 1);
      console.log(`429, sleeping ${wait}s`);
      await new Promise(r => setTimeout(r, wait * 1000));
      continue;
    }
    throw new Error(`HTTP ${res.status}`);
  }
  throw new Error('exceeded retries');
}
```

**Checkpoint** — flood the script with a large CSV; on
the first 429 it sleeps and resumes cleanly.

### 4. Mid-batch recovery checkpoint

Persist progress to `.checkpoint`. On rerun, skip rows
up to the last successful index:

```js
const CHECKPOINT = './.checkpoint';
let start = fs.existsSync(CHECKPOINT)
  ? +fs.readFileSync(CHECKPOINT, 'utf8')
  : 0;

for (let i = start; i < rows.length; i++) {
  const out = await postWithRetry(rows[i]);
  console.log('OK', rows[i].email, out.id);
  fs.writeFileSync(CHECKPOINT, String(i + 1));
}
fs.unlinkSync(CHECKPOINT);
```

**Checkpoint** — kill the script mid-batch with
Ctrl-C, re-run; the final entity count in your tenant
matches the CSV row count. No row is double-written
and none is skipped.

## What you built

A re-runnable import script you can point at any
CSV → entity mapping. The three patterns it
demonstrates (idempotency key, backoff, checkpoint
file) generalize to every long-running batch
integration you'll write against FastYoke.

## Next

[301 — Author an extension](/docs/tutorials/301-author-extension) —
build, sign, and install a React extension that runs
inside the admin shell.
