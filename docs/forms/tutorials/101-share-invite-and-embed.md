---
title: "Share with an invite or embed"
summary: "Mint a kiosk invite link and embed the form on your marketing site."
order: 4
tier: "101"
---

# Share with an invite or embed

::callout{type="info" title="What you'll build"}
Two ways to send visitors at the same `Patient Intake` form: a Mint
invite link for the front-desk kiosk, and an inline embed dropped
into the clinic's marketing site at `/intake`.
::

::callout{type="note" title="Prerequisites"}
Finish [Style and brand your form](/docs/forms/tutorials/101-style-and-brand)
before starting this tutorial. You need the branded `patient-intake`
form already published, with assets and copy overrides saved.
::

A published form already has a public URL. This tutorial is about
the two share paths that sit on top of that URL: a tracked, revocable
invite token for the kiosk, and an SDK-loaded inline embed for your
own pages. Both render the same form; they differ in how the visit
is attributed and how you turn the link off later.

## 1. Mint the front-desk-kiosk invite

From the admin shell, open **Forms** and click into `Patient Intake`.
In the form's right rail, open the **Invites** panel and click **New
invite**.

Fill in the panel:

- **Label.** `front-desk-kiosk`. The label is for your own records —
  it shows up in the list of active tokens so you can tell the kiosk
  link apart from any other invites you mint later.
- **Expiry.** `30 days`. That maps to the panel's default 720-hour
  lifetime. The token stops working at midnight UTC on the 30th day;
  re-mint to extend.
- **Uses.** Leave the submission cap blank for unlimited. The kiosk
  takes one walk-in after another, and a per-token cap would lock
  the iPad out mid-shift.

Click **Mint**. The panel returns a URL of the form
`https://app.fastyoke.io/f/<token>`. Copy it — the raw token is
hashed server-side and there's no way to retrieve it after this
screen closes. Paste the URL into the kiosk browser as a bookmark
or pinned tab.

If you ever need to disable the kiosk link — staff turnover, lost
iPad, anything — open the **Invites** panel, find the
`front-desk-kiosk` row, and click **Revoke**. Visitors who try the
URL after revocation get a `410 Gone` page; submissions already on
file stay intact.

## 2. Embed on the marketing site

Open `Patient Intake` again and switch to the **Embed** panel. Pick
`inline` mode, save the embed, and copy the snippet the panel shows.
It looks like this:

```html
<script async src="https://app.fastyoke.io/embed.js" data-embed="em_AbCd1234567890EfGhIjKl"></script>
```

The `em_` prefix plus 22 alphanumeric characters is the opaque embed
ID assigned when you saved. The snippet loads the FastYoke embed
SDK asynchronously and mounts the form inline at the script tag's
position in the DOM.

Paste the snippet into your clinic's marketing-site `/intake`
Next.js page. JSX accepts the raw `<script>` tag — drop it inline
inside the page component where you want the form to render, and
Next.js renders it through to the client HTML untouched.

Before you ship the page, add the marketing site's origin to
**Settings → Embed domains** in the admin shell. With the allowlist
empty the SDK accepts any origin; once you add an entry, requests
from origins not on the list get `403 Forbidden` and visitors see
the "Form temporarily unavailable" placeholder.

## 3. Pick the path that fits

Invites and embeds both render the same `Patient Intake` form, but
they answer different questions. An invite is a tracked, named
issuance: each submission carries the token, the **Invites** panel
shows a live count, and revocation kills future use with a single
click. Use it for kiosks, one-off campaigns, anything where you
want to attribute or turn off the link later. An embed reaches the
form through the form's public token directly: it lives on a page
you control and is governed by the embed-domain allowlist rather
than a per-issuance record. Use it for stable surfaces — your
marketing site, a customer portal — where the page itself is the
attribution.

## Verify it worked

Open the front-desk-kiosk URL in one incognito window and the
marketing site's `/intake` page in another. Submit a test entry in
each.

In the admin shell, open `Patient Intake` and switch to
**Submissions**:

- The submission from the kiosk URL shows `front-desk-kiosk` in
  the invite-source column.
- The submission from the marketing-site `/intake` page shows no
  invite source — it's unattributed, which is the correct shape
  for the embed path.

If the embed submission is missing entirely, re-check the
embed-domain allowlist. The most common failure is a freshly-saved
embed against an empty or mis-typed allowlist; the **Embed** panel
shows a warning banner with a direct link to the settings page
when no domains are configured.

## Next

Continue with [Map a form to an AcroForm template](/docs/forms/tutorials/201-acroform-template).
