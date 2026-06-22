---
title: Lab M7 — Pages & App Builder
summary: Author a 3-page App in the admin shell — Vehicles list (from M3), Intake form (from M4), Dashboard widget. Set a theme override and a navigation order. Open the App and confirm all three pages render.
order: 14
---

# Lab M7 — Pages & App Builder

This lab pairs with **Module M7 — Pages & App Builder** in
the [syllabus](/docs/training/syllabus).

## What you'll do

Author an App with three pages and a theme override.
Page 1 surfaces the `vehicle` entity list you authored in
M3. Page 2 embeds the form you composed in M4. Page 3
renders a simple dashboard widget showing a record count.
Set navigation order. Open the App and confirm all three
pages render under the overridden theme.

## Before you begin

- An admin session on a tenant you're an admin of.
- The `vehicle` entity from M3 (if M3 wasn't completed,
  any custom entity schema works).
- The public form from M4 (or any form on the tenant).
- About 40 minutes.

## Or use Postman

This lab is GUI work in the admin shell. Postman doesn't apply here — open the admin shell and follow the steps
below.

## Steps

1. **Create a new App.** Settings → Apps → **New App**.
   Name it `Service-Ticket Console`.

   **Checkpoint:** the new App appears in the Apps list.
2. **Set a theme override.** Open the App's theme
   settings and override one color token (e.g., the
   primary accent color).

   **Checkpoint:** the App's edit shell renders in the
   overridden color.
3. **Add Page 1 — Vehicles list.** Add a Page that
   surfaces the `vehicle` entity list as its main block.

   **Checkpoint:** the Page preview shows your `vehicle`
   records.
4. **Add Page 2 — Intake form.** Add a Page that embeds
   the M4 form.

   **Checkpoint:** opening Page 2 in the preview shows
   the form fields.
5. **Add Page 3 — Dashboard.** Add a Page with a single
   widget showing the entity count for `vehicle`.

   **Checkpoint:** the widget displays an integer
   matching `GET /entities/vehicle?count=true` if you
   want to double-check.
6. **Set navigation order.** Order: Vehicles list →
   Intake form → Dashboard.

   **Checkpoint:** the App's nav bar lists the three
   pages in that order.
7. **Open the App from the admin shell.** Navigate to
   the App URL.

   **Checkpoint:** all three pages render under the
   overridden theme; nav order is correct; embedded form
   submits successfully.

## What you'll have at the end

A 3-page App live in your tenant's admin shell with a
theme override visible across all three pages.

## Stuck?

See [Lab M7 — Solution](/docs/training/labs/M7-pages-and-apps-solution).

## Next

[Lab M8 — Yoke Studio](/docs/training/labs/M8-yoke-studio).
