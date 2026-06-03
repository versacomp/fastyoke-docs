---
title: Email Connector (Gmail & Outlook)
summary: Sync customer emails into your CRM, automatically — connect Gmail or Outlook/M365.
---

# Email Connector (Gmail & Outlook)

Connect your Gmail or Outlook/Microsoft 365 account and FastYoke will
automatically attach the emails you exchange with your customers to the
matching CRM records — so every contact, customer, and company carries
its full correspondence history.

## How it works

- **Read-only.** FastYoke requests read-only access to your mailbox. It
  can never send, delete, or modify your email.
- **Customers only.** Only messages involving an address already in your
  CRM (a lead, contact, customer, or company) are stored. Everything else
  is ignored — the contents of non-customer emails are never downloaded.
- **Private by design.** Email subjects and bodies are encrypted at rest.
  Disconnect at any time, or purge every synced email with one click.

## Connecting

1. Open **Yoke → Connectors**.
2. Click **Connect Gmail** or **Connect Outlook** and approve the
   read-only permission in the provider's consent screen.
3. FastYoke begins linking your recent customer emails within a few
   minutes.

You can connect both Gmail and Outlook simultaneously — each mailbox is
synced and stored independently under your account.

## Disconnecting

- **Disconnect** stops syncing but keeps the emails already linked.
- **Purge synced emails** permanently deletes every email FastYoke stored
  from that mailbox. Purge operates per-provider, so purging Gmail does
  not affect your Outlook emails and vice versa.

## Supported providers

| Provider | Account type |
|---|---|
| **Gmail** | Google Workspace or personal Gmail |
| **Outlook / M365** | Microsoft 365, Outlook.com, or Azure AD organizational accounts |
