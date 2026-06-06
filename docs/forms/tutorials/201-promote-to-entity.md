---
title: "Promote submissions into entity records"
summary: "Spawn a job per submission, then fire a transition that copies the submission into a Patient record."
order: 8
tier: "201"
---

# Promote submissions into entity records

::callout{type="info" title="What you'll build"}
Every accepted `Patient Intake` submission now spawns a job in
the `Patient Intake Flow` workflow in state `received`. When
an operator fires the `mark_triaged` transition, the job
advances to `triaged` and a `patient` entity record is created
from the submission's payload — one atomic step, fully audited
in `event_log`.
::

::callout{type="note" title="Prerequisites"}
Finish [Collect signatures](/docs/forms/tutorials/201-signatures)
first. You need the `Patient Intake` form published, with both
PDF templates wired up. This tutorial leaves the form itself
alone — you'll add an entity, an FSM, and a Submit-actions
binding around it.
::

Submissions land in a quarantine zone by design: the
form-scoped tables have no FK path into `entity_records`, so a
malformed or hostile payload can't corrupt your operational
data. Promotion to an entity is an explicit FSM action —
`PROMOTE_FORM_TO_ENTITY` — that an operator fires by name. The
audit trail stays clean because every entity write has a
named transition behind it.

## 1. Create the `patient` entity kind

From the admin shell, open **Entities**:

1. Type `patient` in the "Entity kind" box.
2. Click **Load**. The records list is empty — that's exactly
   what you want. The kind is now registered; the FSM action
   you'll author below will populate it.

Leave the page. You don't need to seed a record by hand.

## 2. Design the `Patient Intake Flow` workflow

Open **Workflows** → **New workflow**. The FSM Designer opens
as a React Flow canvas.

1. Add two states: `received` and `triaged`. Click **Set
   initial** on the `received` node.
2. Drag a transition from `received` to `triaged`. In the side
   panel, set `event_type = mark_triaged`. No guard needed for
   the first run.
3. Name the workflow `Patient Intake Flow`.

Don't save yet — the action wiring comes next.

## 3. Attach the `PROMOTE_FORM_TO_ENTITY` action

Still on the `mark_triaged` transition's side panel, click
**Add action** and pick `PROMOTE_FORM_TO_ENTITY` from the
catalog. Fill the payload template with:

```json
{ "entity_name": "patient" }
```

That's the entire payload. When the operator fires
`mark_triaged`, the action will copy the submission attached
to the job into a fresh `patient` entity record.

::callout{type="tip" title="Where does submission_id come from?"}
You don't have to supply one. The action resolves the
submission automatically from the job it's running on,
because the form's Submit actions (step 6) link the
submission to the job at spawn time. Pass an explicit
`submission_id` only if you need to promote a different
submission than the one linked to the current job.
::

## 4. Bind the workflow to the `patient` entity

In the workflow sidebar, find the **Entity name** field and
type `patient`. This binding is what lets entity annotations
light up the guard composer later, and it's what tells the
FSM Designer which kind to validate the promote action
against. With the binding in place the action's
`entity_name` payload is checked at save time, not at fire
time — a typo here surfaces immediately, not on the next
operator click.

## 5. Save the workflow

Click **Save**. The schema is written with `is_active = true`
and `Patient Intake Flow` shows up in the workflows list. The
`mark_triaged` transition now carries the
`PROMOTE_FORM_TO_ENTITY` action; the dry-run panel will let
you preview the entity write against any existing submission
before you fire it for real.

## 6. Wire `Patient Intake` to spawn jobs

Open `Patient Intake` in the Forms list, then open its detail
view. Find the **Submit actions** panel:

1. In the *Spawn job in schema* dropdown, pick
   `Patient Intake Flow`.
2. Confirm the *Initial state* shows `received` (it should be
   the default — that's the state you marked initial above).
3. Leave the *Link submission to job* toggle on. This is the
   bit that makes the `submission_id`-less promote action
   work; the spawn writes the submission's id onto the new
   job so the action can resolve it back at fire time.
4. Save.

Every accepted submission from this point forward spawns a
fresh `Patient Intake Flow` job in `received`, with the
submission's `processing_state` set to `attached`.
Submissions accepted before this binding existed stay
`pending` and never auto-spawn — they're available for
manual triage from the submissions list.

```text
┌──────────┐  mark_triaged   ┌──────────┐
│ received │ ──────────────▶ │ triaged  │
└──────────┘   action:       └──────────┘
               PROMOTE_FORM_TO_ENTITY
```

## Verify it worked

Open the public URL of `Patient Intake` in an incognito
window. Fill the demographics, contact, and consent pages
from the earlier tutorials and submit.

Head to `/admin/jobs`. A new `Patient Intake Flow` job
appears in `received` within a second of the submit
returning. Click into it — the side panel shows the linked
submission id and the payload you just entered.

Click **Mark triaged**. Three things happen in one atomic
step:

1. The job advances from `received` to `triaged`.
2. The `PROMOTE_FORM_TO_ENTITY` action copies the
   submission's payload into a new record on
   `entity_records` with kind `patient`.
3. The submission's `processing_state` flips from
   `attached` to `promoted` and its `entity_record_id`
   points at the new record.

Open **Entities**, type `patient`, and click **Load**. The
submitter's values show up as a fresh row. Discarded
submissions don't land here; every `patient` record you see
came from a `mark_triaged` an operator explicitly fired,
and the append-only `event_log` carries the full audit
trail of who fired which transition against which job.

## Next

Continue with [Prefill a form from an entity record](/docs/forms/tutorials/201-entity-prefill).
