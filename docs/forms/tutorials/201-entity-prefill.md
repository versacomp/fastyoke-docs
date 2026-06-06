---
title: "Prefill fields from your entity schema"
summary: "Annotate the patient entity once; the form's labels, requireds, and select options come along automatically."
order: 9
tier: "201"
---

# Prefill fields from your entity schema

::callout{type="info" title="What you'll build"}
The `patient` entity gets a small set of annotations — labels,
a date format, an enum list. You re-author `Patient Intake`
using the **From entity** palette so each field carries the
entity's metadata. Edit a label on the entity later and the
admin preview picks it up without rebuilding the form.
::

::callout{type="note" title="Prerequisites"}
Finish [Promote submissions into entity records](/docs/forms/tutorials/201-promote-to-entity)
first. You need a `patient` entity kind registered and the
`Patient Intake` form published.
::

Annotations live on the entity, not the form. That means one
edit to the entity propagates to every form (and every other
LCAP surface) that consumes it. The Forms builder reads those
annotations at import time, and the admin preview reads them
again at render time — so the loop is short.

## 1. Annotate the `patient` entity

Open **Entities**, type `patient`, click **Load**, then open
the **Annotations** tab. Add three annotations:

- `first_name` → label `First name`.
- `dob` → label `Date of birth`, `formatting: "MM/DD/YYYY"`.
- `preferred_language` → label `Preferred language`, with
  enum options `English`, `Spanish`, `Mandarin`, `Other`.

If you'd rather author by hand, the panel accepts JSON:

```json
{
  "first_name":         { "label": "First name" },
  "dob":                { "label": "Date of birth", "formatting": "MM/DD/YYYY" },
  "preferred_language": {
    "label": "Preferred language",
    "enum":  ["English", "Spanish", "Mandarin", "Other"]
  }
}
```

Save. The annotations are now read by both the builder's
**From entity** importer and the admin preview's renderer.

## 2. Re-author the form from the entity

Open `Patient Intake` in the Forms list. In the builder's
field palette, switch to the **From entity** tab and pick
`patient`. The importer lists every field on the entity and
offers to drop them onto the current page. Accept all.

Existing fields with matching keys are replaced — the
importer is destructive on key collisions by design, so the
imported metadata wins. Every imported field gets a
`source.entity = "patient"` provenance stamp; the builder
uses that stamp later to decide which renderer to use.

::callout{type="info" title="What flows through"}
The importer reads each annotation and pre-fills the
field's <code>label</code>, <code>required</code>,{' '}
<code>max_length</code>, <code>min</code>, and{' '}
<code>max</code>. Enum lists are synthesized into a{' '}
<code>select</code> field with the enum values as options —
which is how `preferred_language` lands as a four-option
select without you typing the options into the form.
::

## 3. Watch SmartField pick up live edits

Open the admin preview. Source-stamped fields now render
through the LCAP `<SmartField />` resolver — the label on
`first_name` reads from the entity's annotation, not from a
copy frozen into the form.

Prove it: leave the preview open in one tab, open the
`patient` annotations in another, and rename `first_name`'s
label from `First name` to `Legal first name`. Save. Reload
the preview tab — the label updates. No form re-publish, no
build step.

::callout{type="note" title="Public form is still legacy"}
The SmartField path only fires in the admin preview. The
public form URL keeps the legacy renderer until a
public-annotation read endpoint ships, so a label change
on the entity won't reach public submitters until you
re-publish the form (which freezes the current annotation
text into the version row).
::

::callout{type="tip" title="SmartField allow-list"}
The resolver only takes over when the form/annotation type
pair is on a strict allow-list (<code>text↔string</code>,{' '}
<code>textarea↔longtext</code>, <code>number↔number</code>,{' '}
<code>checkbox↔boolean</code>, <code>date↔timestamp</code>,{' '}
<code>select↔enum</code>, <code>file↔file_ref</code>).
Mismatches and missing annotations fall back to the legacy
renderer with a one-time <code>console.warn</code>. Layout
fields (<code>heading</code>, <code>section</code>,{' '}
<code>static</code>), <code>email</code>,{' '}
<code>radio</code>, and <code>multi_select</code> always
stay legacy.
::

## 4. Save and re-publish

Once the page reads the way you want it to, click **Save**,
then **Publish**. The published version row freezes the
current label and enum text into the public renderer, so
submitters see the same options as your operators do —
until your next annotation change and re-publish.

## Verify it worked

Open the admin preview for `Patient Intake`. Confirm:

- `first_name` shows as `First name`.
- `dob` shows as `Date of birth` with an `MM/DD/YYYY`
  placeholder.
- `preferred_language` shows as a select with `English`,
  `Spanish`, `Mandarin`, `Other`.

Now open the `patient` annotations and rename
`preferred_language` to `Language preference`. Save, reload
the preview. The select's label updates — the options stay
put. That's the loop: edit once on the entity, the admin
preview tracks it.

## Next

Continue with [Conditional routing](/docs/forms/tutorials/301-conditional-routing).
