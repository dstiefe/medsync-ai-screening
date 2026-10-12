---
name: my-patients
description: List the patients screened for a clinical trial, show one patient's screening criterion by criterion, or confirm a completed screening. Use when the clinician asks "who have I screened", "show patient SCR-...", or "confirm that screening".
---

# My patients

Patients are study IDs only; never ask for or repeat a name or identifier.

**Which trial:** call `list_my_trials`. If the clinician already named a trial in this conversation, use it. If they are on one trial, say which and use it. If they are on several and have not said which, show the list and ask.

- **List:** call `list_patients` for the trial and show study ID, latest result (not eligible / provisionally eligible / incomplete), whether it is confirmed, and when. A site investigator sees only the patients they added; a trial creator sees all.
- **One patient:** call `get_patient` and show the latest screening criterion by criterion (status and evidence), then the history of earlier screenings.
- **Confirm:** only when the clinician explicitly says to confirm or finalize a screening, and only for a complete one, call `confirm_screening`. An Incomplete screening cannot be confirmed: say what is missing and offer to run `new-screening` again.
