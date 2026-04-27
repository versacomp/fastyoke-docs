# CI Scripting

> Automate tenant-scoped operations from CI using a long-lived FastYoke API token + curl.

# CI Scripting

FastYoke does not ship a general-purpose API CLI
(no `fy schemas list` / `fy jobs transition` — see the
[CLI reference](/docs/cli) for the full story on why). That
sounds like a gap but rarely matters: the tenant API is REST/JSON,
a FastYoke API token is a single-line bearer credential, and
`curl` is the lingua franca of CI.

This recipe shows the three automations that come up most often:
**minting a CI token**, **provisioning schemas from a checked-in
JSON**, and **bulk-seeding entity records**.

## Mint a tenant API token for CI

Admin-minted API tokens are long-lived, scope-gated, and revocable
without touching a user password. This is the credential shape
you want for pipelines.

<ol>
  <li>Navigate to **Admin → Settings → API Tokens** in the admin shell.</li>
  <li>Click **New token**.</li>
  <li>Name it something that identifies the caller, not the tenant
      — e.g. <code>GitHub Actions – production deploy</code>.</li>
  <li>Select only the scopes the pipeline actually needs. A deploy
      job that runs migrations + seeds entities typically wants
      <code>data:write</code>, <code>workflow:execute</code>, and
      <code>forms:write</code>. Leave <code>admin:*</code> off unless
      you really mean "everything".</li>
  <li>Pick an expiry — 90 days is the recommended default.
      "Never expires" exists but should be paired with out-of-band
      rotation.</li>
  <li>Click **Mint token**, then immediately copy the secret. It
      starts with <code>fy_pat_</code> and is shown exactly once —
      FastYoke stores only the SHA-256 hash.</li>
</ol>

Store the token in your CI provider's encrypted secrets store
(GitHub Actions / GitLab CI / CircleCI all ship one). Never commit
the raw secret to a repo — revoke + re-mint if you do.

```yaml title=".github/workflows/deploy.yml (excerpt)"
env:
  FASTYOKE_TOKEN: ${{ secrets.FASTYOKE_TOKEN }}
  FASTYOKE_TENANT_ID: acme
  FASTYOKE_BASE: https://fastyoke.example/api/v1
```

> **Prefer narrow scopes + short TTL**
>
> Every scope you drop shrinks the blast radius of a leaked token.
>   A read-only deploy-gate job only needs <code>data:read</code> +
>   <code>workflow:read</code>; it should not be carrying
>   <code>admin:*</code> just because that was the default.

> **Still need a browser-shape JWT?**
>
> Some flows (interactive replay, local debugging) want the same
>   JWT a browser session uses. Mint one via
>   <code>POST /api/v1/auth/login</code> — see
>   [Authentication](/docs/auth#1-tenant-user-jwt). API tokens are
>   the right default for unattended pipelines; login JWTs are the
>   right default for people at keyboards.

## Provision schemas from a JSON spec

Version-control your FSM schemas as JSON in the same repo as your
code. A simple CI job then creates (or updates) them on every
merge to main:

```bash title="sync-schemas.sh"
#!/bin/bash
set -euo pipefail

AUTH="authorization: Bearer $FASTYOKE_TOKEN"

for file in schemas/*.json; do
  name=$(jq -r .name "$file")
  existing_id=$(curl -fsS "$FASTYOKE_BASE/tenant/schemas?tenant_id=$FASTYOKE_TENANT_ID" \
    -H "$AUTH" \
    | jq -r ".items[] | select(.name == \"$name\") | .id" \
    | head -1)

  if [[ -n "$existing_id" ]]; then
    echo "Updating schema $name ($existing_id)"
    curl -fsS -X PUT "$FASTYOKE_BASE/tenant/schemas/$existing_id" \
      -H "$AUTH" \
      -H "content-type: application/json" \
      -d "$(jq '{tenant_id: env.FASTYOKE_TENANT_ID, schema_json: .schema_json, name: .name}' "$file")"
  else
    echo "Creating schema $name"
    curl -fsS -X POST "$FASTYOKE_BASE/tenant/schemas" \
      -H "$AUTH" \
      -H "content-type: application/json" \
      -d "$(jq '{tenant_id: env.FASTYOKE_TENANT_ID, name: .name, schema_json: .schema_json}' "$file")"
  fi
done
```

Versioning: each PUT **creates a new version row** — the old
version stays active on in-flight jobs until they finish. Your
git history is the audit log for schema changes; the backend's
`fsm_schemas` append-only table is the audit log for what
was active when.

## Bulk-seed entity records

Useful when migrating from another system, or when setting up
ephemeral tenants for integration tests:

```bash title="seed-entities.sh"
#!/bin/bash
set -euo pipefail

AUTH="authorization: Bearer $FASTYOKE_TOKEN"

jq -c '.[]' seed/vehicles.json | while read -r record; do
  curl -fsS -X POST "$FASTYOKE_BASE/tenant/entities/vehicle" \
    -H "$AUTH" \
    -H "content-type: application/json" \
    -d "{\"tenant_id\":\"$FASTYOKE_TENANT_ID\",\"data_payload\":$record}"
done
```

> **Annotations speed this up**
>
> If your entity has annotations, the picker surfaces +
>   API responses already know the expected field types. Seeding
>   "wrong" types works fine (entity data is schemaless JSON) but
>   downstream Forms v2 pickers won't infer them correctly. Prefer
>   the annotated shape.

## Transition jobs programmatically

For end-to-end tests that spin up a tenant, run a workflow, and
assert on the terminal state:

```bash
# Fire a named transition
curl -fsS -X POST "$FASTYOKE_BASE/tenant/jobs/$JOB_ID/transition" \
  -H "$AUTH" \
  -H "content-type: application/json" \
  -d '{"tenant_id":"'"$FASTYOKE_TENANT_ID"'","event_type":"approve"}'
```

The response includes the new `current_state`. Guards run
server-side via sandboxed JSONLogic (see
[Workflows](/docs/workflows)) — failing a guard returns `409` with
the guard's context so your test can assert on the rejection
reason.

## Error-handling discipline

A tiny helper saves you fifty `if` blocks:

```bash
fy_call() {
  local method=$1 path=$2 body=${3:-}
  local code
  code=$(curl -sS -o /tmp/fy-out -w "%{http_code}" \
    -X "$method" "$FASTYOKE_BASE$path" \
    -H "authorization: Bearer $FASTYOKE_TOKEN" \
    ${body:+-H "content-type: application/json" -d "$body"})
  if [[ $code -lt 200 || $code -ge 300 ]]; then
    echo "fastyoke $method $path → $code" >&2
    cat /tmp/fy-out >&2
    return 1
  fi
  cat /tmp/fy-out
}
```

## Revoking and rotating tokens

Revoke from **Admin → Settings → API Tokens** — click the token's
**Revoke** row button, type the token's name to confirm, and any
in-flight CI job starts getting `401`s on its next request. There
is no un-revoke; mint a replacement.

For periodic rotation, the cleanest pattern is **double-rolling**:
mint the new token, push it to the CI secret store, let the next
pipeline run pick it up, **then** revoke the old one. That avoids
a window where the pipeline has neither credential.

## Scope denials

When an API token hits an endpoint its scope grant doesn't cover,
the backend returns `403 Forbidden` with a body like:

```json
{ "error": "api token scope 'workflow:admin' required but not granted" }
```

This is a feature, not a config error — add the missing scope to
the token's grant (requires re-minting, since scopes are fixed at
mint time) or narrow the pipeline to stay within the token's
original permissions.

## Rate limits + retries

Tenant-scoped endpoints aren't rate-limited today (modulo common-
sense platform protections). Public-form endpoints ARE rate-limited
per invite token — see the [webhook intake recipe](/docs/recipes/webhook-intake)
for the 429-specific error shape if you hit it.

## Related

- [Authentication](/docs/auth) — full token shapes + the scope vocabulary.
- [Webhook Intake](/docs/recipes/webhook-intake) — the inverse
  direction (external system posts in).
