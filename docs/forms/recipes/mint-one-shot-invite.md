---
title: Mint a one-shot invite link
summary: Single-use, expires when consumed — perfect for a personal email.
order: 7
recipe: true
---

# Mint a one-shot invite link

**When you'd use this — you're sending the form to one named person over email, and you want the link to die the moment they submit so it can't be forwarded or replayed.**

A one-shot invite is just a regular invite token with `max_submissions` set to `1`. After the first successful submission the public URL flips to `410 Gone` automatically — no manual revoke needed.

## 1. Open Invites and create a new one

In the admin shell, open the form, switch to the **Invites** tab, and click **New invite**. Fill in:

- **Label** — the recipient, e.g. `jane@example.com`. This is the only place you'll see who the link was minted for, so keep it specific.
- **Uses** — `1`.
- **Expiry** — `7 days` is a sane default for a personal email; raise or lower to taste. Solo accounts cap at 30 days.

Click **Mint**. The dialog reveals the share URL of the form `https://app.fastyoke.io/f/<token>`. Copy it now — the token is hashed in the database and cannot be retrieved later.

## 2. Send it from your normal email tool

Paste the URL into whatever you'd normally use — Gmail, your CRM, a Loom follow-up. Nothing on the recipient's side knows or cares it came from FastYoke.

## 3. After submission the link returns 410 Gone

The submission counter is incremented atomically with the cap check. The first submit succeeds and writes a `form_submissions` row; any subsequent load or submit at that URL returns `410 Gone` with the message "This invite has expired or reached its submission limit." If you need to give the same person a second try, mint a fresh invite — there is no way to reset the counter on an exhausted token.

## See also

- [Share with an invite or embed](/docs/forms/tutorials/101-share-invite-and-embed)
- [Invites reference](/docs/forms/invites)
