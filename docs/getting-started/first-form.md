# Your First Form

> Build a public form, collect submissions, spawn jobs from them.

# Your First Form

By the end of this page you'll have a public URL that anyone can
submit, and every submission will create a job in your FSM and end
up as a first-class entity record. Six steps.

> **What you**
>
> A tiny <code>intake</code> form with two fields (name + notes), a
>   two-state FSM (<code>received → processed</code>), an invite link
>   that spawns a job in <code>received</code> on every submit, and a
>   transition action that promotes the submission to an
>   <code>intake</code> entity record when an operator fires it.

## 1. Create the entity kind

Jobs need an entity record to carry their payload. From the admin
shell, open **Entities**:

1. Type `intake` in the "Entity kind" box.
2. Click **Load**. The list is empty — that's fine.
3. Use the **Create record** form to add one seed record:
   `{ "name": "sample", "notes": "from Installation walkthrough" }`.
4. Save.

You now have one record of kind `intake`. The Fleet-of-Files architecture
stores it in your tenant's own SQLite file, not a shared table.

## 2. Design the FSM

Open **Workflows** → **New workflow**. The FSM Designer opens as a
React Flow canvas.

1. Add two states: `received` (initial) and `processed`. Click
   **Set initial** on the `received` node.
2. Drag a transition from `received` to `processed`. In the side
   panel, set `event_type = mark_processed`. No guard needed for
   the first run.
3. Still on the transition's side panel, click **Add action** and
   pick `PROMOTE_FORM_TO_ENTITY`. Fill the payload template with
   `{ "entity_name": "intake" }` — when the operator fires this
   transition, the submission attached to the job will be copied
   into an `intake` entity record.
   
> **Where does submission_id come from?**
>
> You don't have to supply one. The action resolves the
>      submission automatically from the job it's running on, as
>      long as the form bound its Submit actions in step 4 below.
>      Pass an explicit <code>submission_id</code> only if you need
>      to promote a different submission than the one linked to the
>      current job.

4. Name the workflow `Intake Flow` and bind it to the `intake`
   entity (sidebar "Entity name" field) so
   [entity annotations](/docs/entities) can light up the guard
   composer later.
5. **Save**.

You can publish additional versions later; the saved schema is
automatically marked `is_active`.

## 3. Build the form

Open **Forms** → **New form**:

1. Name it `Intake`, slug `intake`.
2. Drag in two text fields: `name` and `notes`. Mark `name` as
   required.
3. Save. Click **Publish** once you're happy.

> **Mirror your entity schema**
>
> The "From entity" palette tab auto-samples 50 records of the
>   selected kind and offers to import each field with its derived
>   type. If you've annotated the entity (see the
>   <a href="/docs/entities">Entities</a> section), labels, options,
>   and required flags come along for the ride.

## 4. Wire the form to the FSM

Open the form's detail view, find the **Submit actions** panel, and
pick your `Intake Flow` schema from the <em>Spawn job in schema</em>
dropdown. Save.

Every accepted submission will now spawn a fresh `Intake Flow` job
in its `initial_state` with the submission linked to the new job
(`processing_state = 'attached'`). Submissions made before the
binding was set stay `pending` for manual triage.

## 5. Mint an invite token

From the form's detail page:

1. Click **Create invite**.
2. Pick an expiry (default 7 days) and an optional submission cap.
3. Copy the URL — it looks like `/f/<token>`.

Open the link in an incognito window. Fill the form. Submit. Head
back to `/admin/jobs` and you'll see a new `Intake Flow` job in
`received`.

## 6. Fire the transition and land an entity record

Click the new job on `/admin/jobs`, then click **Mark processed**.
Three things happen in one atomic step:

1. The job advances from `received` to `processed`.
2. The `PROMOTE_FORM_TO_ENTITY` action copies the submission's
   payload into a new `intake` record on
   <code>entity_records</code>.
3. The submission's `processing_state` flips from `attached` to
   `promoted` and its `entity_record_id` points at the new record.

Open **Entities**, type `intake`, and click **Load** — the
submitter's values (`name`, `notes`) show up as a fresh row.
Discarded submissions don't land here; every entity record you see
came from a transition an operator explicitly fired, and the
append-only <code>event_log</code> carries the full audit trail.

## What's next

- [Forms Builder](/docs/forms/builder) — multi-page forms, routing,
  attachments, custom themes.
- [FSM Designer](/docs/workflows/builder) — guards, actions, action
  payload templates.
- [Entities](/docs/entities) — annotations, the Page Designer, and
  entity-scoped pages.
