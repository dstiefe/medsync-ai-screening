---
name: my-patients
description: List the patients in a clinical trial, show one patient's screening, or enroll an eligible patient once consent is signed. Use when the clinician asks "who have I screened", "show patient ...", or "enroll / admit that patient".
---

# My patients

Patients are study IDs only; never ask for or repeat a name or identifier.

**Which trial:** call `trials_list`. If the clinician already named a trial in this conversation, use it. If they are on one trial, say which and use it. If they are on several and have not said which, show the list and ask.

## What to do

- **List:** call `trials_patients`. It returns three groups: `in_process` (not finished, or eligible and not yet enrolled), `failed` (with the criterion) and `in_trial` (enrolled). Show them with each patient's study ID and the reason the server gives. A doctor sees only their own patients; a trial admin sees all of the trial's.
- **One patient:** call `trials_patient`. Show the verdict, the answers saved so far with their evidence (which came from Claude and is unverified), which criteria are still unanswered or failed, whether the patient is enrolled, and the titles of any saved notes. To carry on with a paused screening, open the form with `trials_open_form` and the `patient_id` (see the `new-screening` skill).
- **Verdict:** the server's rule engine decides it. Tell the clinician what the server returned and let them decide. You never confirm a screening yourself.
- **Enroll:** only for a patient whose latest screening is eligible (or eligible pending), and **only after the clinician tells you the patient's informed consent has been signed**. Ask them if they have not said; never assume it. Then call `trials_enroll_patient` with `consent_signed=true`. Leave `subject_id` out and the server assigns the next one. Enrollment is the clinician's decision: call it only when they say to.
