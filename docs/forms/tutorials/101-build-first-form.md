---
title: "Build your first form"
summary: "Create the Patient Intake form — two pages, six fields, publish."
order: 2
tier: "101"
---

# Build your first form

::callout{type="info" title="What you'll build"}
A published `Patient Intake` form with a demographics page and a
contact page, ready to share. Submissions will land in the admin
queue.
::

::callout{type="note" title="Prerequisites"}
You should already have a tenant up and running. If you don't,
finish [installation](/docs/getting-started/installation) and
[signup](/docs/getting-started/signup) first, then come back.
::

This is the first stop on the 101 → 201 → 301 path. Each step takes
about a minute. Use the names exactly as written — later tutorials
build on this form by slug and field key, so renaming things now
will trip you up later.

## 1. Create the form

From the admin shell, open **Forms** and click **New form**.

1. Set the name to `Patient Intake`.
2. Set the slug to `patient-intake`. The slug is the public URL
   fragment, so keep it lowercase and hyphenated.
3. Click **Create**. The builder opens with one empty page.

## 2. Add the demographics page

Rename the first page to `demographics` in the left rail. Drag the
following fields into the page in this order, marking the required
ones:

- `first_name` (text, required)
- `last_name` (text, required)
- `dob` (date, required)
- `preferred_language` (select, optional)

The field key is what the submission payload uses — the label on
screen can be whatever reads cleanly, but the key stays
machine-friendly.

## 3. Configure the language select

Click `preferred_language` to open its side panel and add four
options. The saved field looks like this:

```json
{
  "type": "select",
  "key": "preferred_language",
  "options": ["English", "Spanish", "Mandarin", "Other"]
}
```

You can edit the JSON directly in the **Advanced** tab if you'd
rather skip the form — both views write the same field row.

## 4. Add the contact page

Add a second page from the left rail and name it `contact`. Drag in
these six fields:

- `email` (email, required)
- `phone` (text)
- `street` (text)
- `city` (text)
- `state` (text)
- `postal_code` (text)

Only `email` is required on this page. Leave the others optional —
not every submitter will have a full mailing address, and the form
should still accept what they can give you.

## 5. Save and publish

Click **Save** to persist the draft, then **Publish** to mint the
public version. A `draft` form is visible only in the admin; once
published, the slug `patient-intake` resolves to a public URL the
admin shell shows in the form's detail header.

```text
┌──────────────┐    ┌─────────┐    ┌────────┐
│ demographics │ -> │ contact │ -> │ submit │
└──────────────┘    └─────────┘    └────────┘
```

The renderer walks the pages in order. Each page validates its own
fields before the **Next** button advances. The final page swaps
**Next** for **Submit**.

## Verify it worked

Copy the public URL from the form's detail header in the admin
shell and open it in an incognito window. Fill in plausible values
on both pages — anything that satisfies the required fields will
do — and click **Submit**.

Back in the admin shell, open **Forms** → **Submissions** and pick
`Patient Intake` from the form filter. Your test row should be at
the top of the list, with the demographics and contact values you
typed visible in the row detail.

If the submission isn't there, check that you clicked **Publish**
and not just **Save** — a draft form has no public endpoint and
the URL will 404.

## Next

Continue with [Style and brand your form](/docs/forms/tutorials/101-style-and-brand).
