---
title: Schedule Explorer
summary: App Extension — drag-and-drop scheduling, swimlanes, and a capacity simulator, backed by your FastYoke data.
order: 6
---

# Schedule Explorer

**Schedule Explorer** is an **App Extension** — an external application that runs on its own and uses your FastYoke workspace as its data backend. Unlike the in-FastYoke apps, an App Extension opens in a new browser tab and talks to FastYoke over the API using a dedicated key.

## What it does

Schedule Explorer gives you drag-and-drop scheduling, swimlane and resource-timeline views, and an agent-based capacity simulator — all reading and writing the records and workflows in your FastYoke tenant.

## How an App Extension works

1. A FastYoke administrator grants Schedule Explorer to your account.
2. You install it from **Logic Cores** → the **Extensions** group.
3. On install, FastYoke creates a **Launch** page in your workspace and mints a dedicated API key for the extension. **The key is shown only once** — copy it immediately.
4. You (or your operator) paste the key into the external Schedule Explorer deployment so it can reach your data.
5. Open the **Launch** page in FastYoke and click **Open Schedule Explorer ↗** to start the app in a new tab.

## Installing from Logic Cores

1. Go to Logic Cores (sidebar → **Logic Cores**).
2. Find **Schedule Explorer** in the **Extensions** group.
3. Click **Install**.
4. Copy the API key shown in the install confirmation — it is displayed once and never again.
5. The **Launch** page now appears in your workspace.

## Connecting the key

Schedule Explorer reads two environment variables in its own deployment:

- `FASTYOKE_API_KEY` — the key minted at install (shown once on the install confirmation).
- `FASTYOKE_TENANT_ID` — your tenant identifier.

Set both in the external deployment's environment, then open the **Launch** page and click **Open Schedule Explorer ↗**.

## Uninstalling

Uninstalling Schedule Explorer removes the Launch page and **revokes the API key** — the external app immediately loses access to your data. Reinstalling mints a fresh key (the old one is never reused), so you will copy and configure a new key.

## Permissions and isolation

The minted key is scoped to your tenant and to the data and workflow operations the app needs (read and write records, run workflows, read and write files). It cannot perform administrative actions. The key only ever resolves to your tenant — no other tenant can use or see it.
