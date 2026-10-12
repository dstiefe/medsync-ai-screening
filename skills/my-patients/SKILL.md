---
name: my-patients
description: List the patients in a clinical trial by stage, show one patient's screening criterion by criterion, confirm a completed screening, or enroll a confirmed-eligible patient once consent is signed. Use when the clinician asks "who have I screened", "show patient ...", "confirm that screening", or "enroll / admit that patient".
---

# My patients

Patients are study IDs only; never ask for or repeat a name or identifier.

**Which trial:** call `list_my_trials`. If the clinician already named a trial in this conversation, use it. If they are on one trial, say which and use it. If they are on several and have not said which, show the list and ask.

## Stages

Every patient is in exactly one stage; the server decides it.

| Group | Stage | Meaning |
|---|---|---|
| Pending | **In progress** | a draft saved part-way |
| Pending | **Needs info** | all criteria looked at, but some still unknown or awaiting the investigator |
| Pending | **In review** | eligible, waiting for the clinician to confirm |
| | **Confirmed eligible** | the clinician confirmed they qualify; consent not yet recorded |
| | **Admitted** | consent signed and enrolled |
| | **Not eligible** | an exclusion is met or an inclusion is not met |

## What to do

- **List:** call `list_patients` and show the patients **grouped by those stages, Pending first**, each with its study ID and the `reason` the server gives (for example "exclusion 3 met", "Still open: exclusion 8", "14 of 25 criteria answered"). A site investigator sees only the patients they added; a trial creator sees all.
- **One patient:** call `get_patient` and show the stage, then the latest screening criterion by criterion (status and evidence), then the history. For a draft, also list the criteria **not yet assessed** and offer to carry on with the `new-screening` skill using the same study ID.
- **Confirm:** only when the clinician explicitly says to confirm or finalize a screening, and only for a complete one, call `confirm_screening`. A draft or an Incomplete screening cannot be confirmed: say what is missing and offer to carry on.
- **Enroll (admit):** only for a patient who is **Confirmed eligible**, and only after the clinician **tells you the patient's informed consent has been signed**. Ask them that question if they have not said it; never assume it. Then call `enroll_patient` with `consent_signed=true`. The patient moves to **Admitted**. If the patient is not Confirmed eligible, say which stage they are in and what has to happen first.
