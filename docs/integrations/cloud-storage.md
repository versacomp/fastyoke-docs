---
title: Cloud Storage Connector (OneDrive & Dropbox)
summary: Automatically deposit your signed documents and form PDFs into your own OneDrive or Dropbox folder.
---

# Cloud Storage Connector (OneDrive & Dropbox)

Connect your OneDrive or Dropbox account and FastYoke will automatically
drop a copy of your finished documents into a folder you control — so your
signed agreements and form submissions live in the same cloud drive your
team already uses, without anyone re-downloading and re-uploading by hand.

## What gets pushed

- **Signed documents.** When a document is sealed, its Certificate of
  Completion is delivered to your drive.
- **Form PDFs.** When a form submission is rendered to PDF, that PDF can be
  delivered too. (Off by default — turn it on per connection.)

## How it works

- **One-way export.** FastYoke only *adds* files to your drive. It never
  reads, lists, modifies, or deletes anything already there.
- **Automatic.** Once a drive is connected, every new signed document lands
  in your folder on its own. No manual step.
- **Organized for you.** Files are filed under your chosen root folder, then
  by type and month — for example `/FastYoke/e-sign/2026-06/`. You pick the
  root; FastYoke keeps it tidy from there.
- **No duplicates.** Each document is delivered once. If a delivery fails
  (a network hiccup on the provider's side), it's recorded so you can
  re-send it with a single action.

## Connecting a drive

1. Open **Connectors** in your workspace settings.
2. In the **Cloud Storage** section, choose **Connect OneDrive** or
   **Connect Dropbox** and approve access in the provider's window.
3. Back in FastYoke, set the **root folder** (defaults to `/FastYoke`) and
   choose what to push: signed documents, form PDFs, or both.

You can connect more than one drive, and disconnect any of them at any time.

## Availability

The Cloud Storage Connector is available on Pro plans and above. If you
don't see the Cloud Storage section under Connectors, ask your administrator
about enabling it for your workspace.
