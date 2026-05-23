---
title: Extensions (Install & Manage)
summary: Upload third-party extension bundles, review scopes, activate, roll back.
order: 7
---

# Extensions (Install & Manage)

This page is for the tenant admin **installing** an extension
someone else built. For the author's side — scaffolding, iterating,
publishing — head to the
[Extension Authoring recipe](/docs/recipes/extension-authoring).

::callout{type="info" title="Two directions, same table"}
Uploaded bundles land in the per-tenant <code>tenant_extensions</code>
  table regardless of whether they got there via <code>fy publish</code>
  (remote CI) or the <strong>Upload</strong> button in this page
  (admin browser). The install flow is identical from there.
::

::section-cards{section="extensions"}
::

## Upload flow

From `/admin/extensions`:

1. Click **Upload**.
2. Pick a `.zip` containing `manifest.json` + `dist/bundle.mjs`.
3. The three-layer security scanner runs (MIME magic →
   [VirusTotal](https://virustotal.com) hash lookup → LLM content
   inspection). A **Malicious** verdict rejects the upload
   outright. **Suspicious** / **Skipped** pass with a visible
   badge on the row.
4. On success the extension is installed and **active**
   (`is_active = 1`).

SHA-256 of the bundle is recorded alongside the row so you can
cross-check against the author's published checksum.

## Reviewing scopes before activation

Every manifest declares `required_scopes`, e.g.
`["entities:read", "jobs:read"]`. The admin shell shows the
declared set prominently in the upload confirmation dialog.
Reject the upload if the scopes don't match what you expect from
the extension's documented purpose.

::callout{type="warn" title="Scopes are enforced at runtime"}
Every API call carrying an extension-scoped JWT or API token is
  checked against the manifest's <code>required_scopes</code> at
  the request boundary; a missing scope rejects the call with HTTP
  403. Review the scope list during install — it's both a trust
  signal and the active security boundary. The extension still
  runs under the signed-in user's JWT, so its effective permissions
  are a subset of the user's regardless of declared scopes.
::

See the full [scope vocabulary](/docs/auth#scopes) in the Auth page.

## Versioning

Uploads are keyed by `(tenant_id, extension_id, version)`. The
important rules:

- Same `(extension_id, version)` uploaded twice → **409 Conflict**.
  You can't overwrite history.
- Different version of the same `extension_id` → the previous
  active row flips to `is_active = 0`; the new one lands active.
  Only one version of a given extension is active at a time per
  tenant.
- Historical rows stay in the DB forever — the ledger is your
  rollback source.

The version ledger is append-only. There is no "delete this
version" action; uninstall below flips `is_active = 0` but keeps
the row.

## Rolling back

The detail view for an extension id shows every uploaded version
with its `created_at`, actor, and scan verdict. To roll back:

1. Click the desired historical version.
2. **Activate** — flips the current row to `is_active = 0` and
   the selected one to `is_active = 1` in one transaction.
3. The UI reloads pointing at the newly-active bundle.

No bytes are re-uploaded; the blob in the old row is served
directly from `tenant_extensions.bundle_blob`.

## Uninstalling

"Uninstall" means setting `is_active = 0` on every row for the
extension id. The bundle blobs stay in the DB (for potential
future re-activation + audit). If you need to remove the bytes
outright, that's a platform-admin operation — not exposed in
the tenant admin shell.

## What an installed extension can do

Depending on the manifest:

- **Pages** — mount at `/ext/:ext_id/<path>`. Show up in the
  admin nav under an **Extensions** heading.
- **Components** — custom block types available in the
  [Page Designer](/docs/pages) palette.

Every extension runs inside a
[`FastYokeProvider`](/docs/sdk/reference) with a short-lived
15-minute JWT carrying `ext_id` + `scopes`. The host provides
`react`, `react-dom`, and `@fastyoke/sdk` via the import map so
every extension shares one React instance + one SDK client with
the shell — context-based state (current user, tenant, feature
flags) crosses cleanly.

## Troubleshooting

| Symptom | Cause |
|---|---|
| Upload rejected: "bundle rejected by security scan" | Scanner flagged the bundle. Ask the author to re-scan locally + compare; often a third-party dep with heuristic-positive code. |
| Component renders blank | Host/extension React instance mismatch. See the [authoring recipe's dev-mode note](/docs/recipes/extension-authoring). |
| 409 on re-install | Version already uploaded. Ask the author to bump `manifest.json → version`. |

## Related

- [Extension Authoring](/docs/recipes/extension-authoring) — the
  author's side of the same contract.
- [SDK Reference](/docs/sdk/reference) — the hooks + components
  an installed extension can use.
- [Authentication → extension JWT](/docs/auth#4-extension-jwt) — the
  token shape that backs an installed extension's API calls.
