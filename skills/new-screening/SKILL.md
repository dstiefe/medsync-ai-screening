---
name: new-screening
description: Start a screening of a patient for a clinical trial. Use when the clinician says "new screening", "screen a patient", "add a patient", "does this patient qualify", or pastes or attaches patient notes, reports or images for a trial.
---

# New screening

The MedSync trials server holds the criteria, applies the rules and decides the result. You open the form, and read documents only when the clinician gives you some. These steps match the server's own instructions; if they ever differ, follow the server.

## Rules that never bend

- **No identifiers.** The patient is a study ID only. Never put a name, initials, date of birth, record number, phone, email or address into any tool call, including evidence and notes. If documents show any, do not repeat them and tell the clinician to keep them out. Use ages in years and relative times ("3 hours from onset").
- **Silence is not "no".** Send only what a document or the clinician actually states. Never invent a value.
- **You never decide eligibility.** The server's rule engine does. Never confirm or enroll without the clinician saying so.

## Steps

1. **Which trial.** Call `trials_list`.
    - The clinician named the trial: use it, and say which in one line. Do not ask again.
    - They are on one trial: say so and use it.
    - They are on several and did not say which: show the list and ask which one. Ask nothing else first.
    - They are on none: say so and tell them to ask the trial's administrator to add them.
2. **No patient information: open the form at once.** If the clinician only says they want a new screening (or want to test one) and has given no notes or details, do **not** ask for details, and do **not** list questions in the chat. Call `trials_open_form` with **only** the `trial_id`. The blank form opens, and they fill it in and press Submit themselves. Offer to read notes or reports only **after** the form is open.
3. **They gave documents or details.** Call `trials_screening_packet` for the trial (the questions, their field keys, and how to answer each). Read everything they pasted or attached yourself; the server never receives the files.
4. **Start the screening.** Call `trials_start_screening` with **only** the answers a document or the clinician actually states, each as `{field_key: {value, evidence, stated_by_clinician}}`:
    - `evidence` is a short quote that names the document, or "stated by the clinician".
    - A field marked `ask_the_clinician` is sent only when the clinician said it (`stated_by_clinician` true).
    - Skip criteria that have `derived_from`; they come from other answers.
5. **After it opens.** The server saves the patient and the form opens for the clinician to check, change, complete and submit. Tell them what you filled in and what is left for them. Do **not** call `trials_save_screening` for the same answers afterwards.
6. **Reopen a patient.** Call `trials_patients`, let them pick, then `trials_open_form` with the `patient_id`. To see what is saved for one patient, call `trials_patient`; to reread a note that was saved earlier, call `trials_get_note` with its `document_id` instead of asking the clinician to upload the document again. The note is the de-identified text only, not the original file; if it comes back `withheld`, tell them to read it in the MedSync app.
7. **If the form is not shown** (a client without card support), use `trials_save_screening` instead and tell the clinician the server's verdict and what is still needed.
8. **Save the documents with the patient.** After the form is open, for each document you read, call `trials_add_note` with the patient's `patient_id` and a short `title` (for example "ED note", "CTA report"). The files and images are never stored, so the note is the only record of what they said:
    - **A note, report or lab:** send its clinically relevant text, copied faithfully, with **names, initials, dates of birth, record numbers, phone numbers, addresses and calendar dates removed** (write times relative to onset, for example "3.5 hours from onset").
    - **An image:** write down what you can see, and say in the text that it is your reading of the image, not the picture.
    - If the server refuses a note because it looks like it contains an identifier, remove the identifying part and try again.
    - Tell the clinician plainly: "I saved the text of N documents with this patient; the original files are not stored."
9. **Answering the rest in chat (only when the clinician wants that).** After you have read documents or the clinician has given details (step 4), offer one line: "I can ask you the remaining questions here, or you can finish them on the form." Do this **only** if they gave you something to start from, or if they ask you to ask. If they gave you nothing (step 2), do not ask questions; the blank form is theirs to fill in. If they choose chat:
    - **Gather everything still open** (the questions with no answer yet, from `trials_screening_packet` and what `trials_patient` shows) into one grouped set of questions, and keep going until each is settled or the clinician says to leave it open.
    - **Name the criterion and its exact threshold, and say why it matters:** "Inclusion 4 needs an NIHSS of 6 or more: what was the score?" Ask for the number or the fact, not "is it mild?".
    - **Keep questions neutral.** Never phrase one so that a particular answer would make the patient eligible. Ask "Is there any history of atrial fibrillation?", not "No atrial fibrillation, right?".
    - **Ask again when an answer is vague** ("I think so", "probably not"). Ask a narrower follow-up, or leave it unanswered. If the answer does not fit the criterion's wording (a different scale, range or time window), say so and ask for what the criterion needs.
    - **When an answer conflicts with a document, show both** side by side and ask which is right. Record the clinician's answer and mention the conflict in the evidence. Do not quietly pick one.
    - **A document's own conclusion is not evidence.** A note saying "meets criteria" settles nothing.
    - **A criterion that can only be judged later** (during or after a procedure, or after a test not yet done) stays unanswered until that has happened; say what has to happen first.
    - **Record each answer** with `trials_save_screening`, passing the `patient_id` (new answers are merged in, so save as you go). Use `"stated by the clinician"` as the evidence and `stated_by_clinician` true. After each answer, say how the result changed (the server returns the verdict and what is still needed) and what is still open.
    - **The form above does not update itself.** A form that is already open in the chat never refreshes when you save answers with `trials_save_screening`. When the chat questions are done (or the clinician asks to see the form), call `trials_open_form` with the `trial_id` and `patient_id`; the new form starts from everything saved. Tell the clinician plainly that the earlier form above is out of date, to use the newest one, and to press Submit on it. Ask them not to answer the same patient in the chat and in an old form at the same time: answers typed into an old form without Submit are not merged into anything.
    - Tell the clinician the verdict the **server** returned. You never decide eligibility.
10. **Enrolling** is a separate step; see the `my-patients` skill.
