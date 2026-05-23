---
title: Integrations
summary: Encrypted credential storage for outbound webhooks, Twilio, SendGrid, and custom REST endpoints.
order: 9
---

# Integrations

Integrations are the glue between your FSM transitions and the
outside world. Store a credential once in `/admin/integrations`, then
fire it as a `WEBHOOK` action from any transition — the action
engine decrypts the credential at execution time and POSTs to the
target system.

::section-cards{section="integrations"}
::

## Supported providers

Three today, all shaped identically on the wire (`{ provider,
connection_name, credentials }`):

| Provider | Typical credentials |
|---|---|
| `twilio` | `{ "account_sid": "...", "auth_token": "..." }` |
| `sendgrid` | `{ "api_key": "..." }` |
| `generic_rest` | `{ "base_url": "https://api.example.com", "auth_header": "Bearer ..." }` |

Unknown providers are rejected at validation time. The list lives in
`connections.rs::VALID_PROVIDERS` — adding a new provider is a
backend change (new handler in the action worker, new validator
entry), not a config toggle.

## Credential storage

Credentials are encrypted at rest with **AES-256-GCM** using
`ENCRYPTION_KEY` (64-char hex) loaded at boot.

- The plaintext is **never** returned by any API. The `GET` and
  `LIST` handlers omit the field entirely — only the connection
  metadata (id, name, provider) round-trips.
- The only place the plaintext exists at runtime is the action
  worker, which decrypts just-in-time when it fires a `WEBHOOK`
  action.
- Rotating the encryption key is a breaking change: every stored
  credential becomes unreadable. There's no re-encrypt-in-place
  tool today — plan for that with a one-shot migration when it
  matters.

::callout{type="warn" title="ENCRYPTION_KEY is not optional"}
Booting without <code>ENCRYPTION_KEY</code> falls back to a
  zeroed dev key. Every deployment that carries real credentials
  must set a real 64-char hex key via
  <code>fly secrets set ENCRYPTION_KEY=...</code> — the
  <code>/auth/me</code> hydrator won't warn you, but the first
  real write will persist ciphertext that only the zero-key can
  decrypt, stranding you on deploy rotation.
::

## Creating a connection

From the admin shell at `/admin/integrations`:

1. Click **New connection**.
2. Pick a provider and a human-readable `connection_name` (you'll
   reference it by id from the FSM designer).
3. Paste the credentials object. The form validates the shape
   per-provider before submission.
4. **Save**.

The HTTP surface is `POST /api/v1/tenant/connections`. Auth +
tenant scoping follow the usual
[CurrentUser](/docs/auth#tenant-user-jwt) rules.

## Firing a WEBHOOK from an FSM transition

Transitions can carry an ordered list of actions
(`action_type: "WEBHOOK"` among others). Add one from the
transition side-panel in the
[FSM Designer](/docs/workflows/builder#actions). The payload template
is a JSON blob with `{{variable}}` placeholders resolved against the
job's context at fire time:

```json
{
  "connection_id": "<your connection row id>",
  "path": "/hooks/fastyoke/job-started",
  "body": {
    "job_id": "{{job.id}}",
    "tenant_id": "{{tenant.id}}",
    "state": "{{job.current_state}}"
  }
}
```

The worker reads the connection, decrypts, and POSTs:
- For `generic_rest`, the `path` is appended to the stored `base_url`.
- For `twilio`, `sendgrid`, the path maps to the provider's
  published API (e.g. Twilio's `/Messages.json`).
- Authentication headers are injected from the decrypted credential
  blob — no need to template them into the payload.

## Metering

Every fired `WEBHOOK` writes a `utility_ledger` row tagged
`WebhookEgress`. That's how per-org metering
charges for integration usage. There's no per-credential rate
limiting today — upstream providers' own rate limits are the
binding constraint.

## Error semantics

The action worker's policy for outbound failures:

- **2xx** — action recorded as success, next action in the list
  fires.
- **4xx / 5xx** — action recorded as failure with the provider's
  response body in the audit log. The FSM transition itself is
  still considered successful (state change commits); the failure
  is surfaced via the `event_log` row but does NOT roll back.
- **Network error / timeout** — same as 5xx from the ledger's
  perspective.

If your workflow requires "transition blocked on webhook success",
pair the WEBHOOK with a guard that checks `event_log` in a
follow-up transition. The single-transition-as-atomic-commit
semantics are deliberate — they keep FSM audit trails straight-
forward.

## Inbound webhooks (reverse direction)

For external systems POSTing INTO FastYoke, see the
[webhook intake recipe](/docs/recipes/webhook-intake) — uses
public forms + invite tokens rather than the Integrations
surface.

## Related

- [Workflows — FSM Designer](/docs/workflows/builder) — adding
  `WEBHOOK` actions to a transition.
- [Webhook intake recipe](/docs/recipes/webhook-intake) — the
  inverse direction.
- [Authentication](/docs/auth) — how tokens gate the CRUD calls
  against `/api/v1/tenant/connections`.
