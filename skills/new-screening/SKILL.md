---
name: new-screening
description: Add a patient to a clinical trial screening and determine whether they meet EVERY inclusion and exclusion criterion. Use when the clinician says "new screening", "add a patient", "does this patient qualify", or pastes or attaches patient notes, reports or images for a trial.
---

# New screening

Add one patient to one trial and decide, criterion by criterion, whether they qualify. You do the reading and the judging; the trials server supplies the criteria and protocol text, enforces the rules, and stores the result.

## Rules that never bend

- **No identifiers.** The patient exists only as a **study ID** (letters, digits, `-` or `_`, no spaces, e.g. `SCR-TEST-01` or a server-assigned `EVEREST-0003`). Never write a name, initials, date of birth, MRN, phone, email or address anywhere, including in evidence and notes. If the documents or images show any, do not repeat them, and tell the clinician to keep them out. Use ages in years and **relative times** ("3 hours from onset"), not calendar dates.
- **Silence is not "no".** If the documents do not say, the status is `unknown`. Never mark an exclusion `not_met` just because it is not mentioned.
- **Quote the evidence** for every `met` / `not_met` call, and say which document it came from. If the clinician told you the answer, the evidence is "clinician stated: …".
- **Every criterion gets answered**: all exclusion and all inclusion. The server refuses a screening that leaves one out.
- **You propose, the clinician decides.** Never save or confirm without the clinician having seen the checklist.

## Statuses

| Status | Exclusion criterion | Inclusion criterion |
|---|---|---|
| `met` | the exclusion applies to this patient | the criterion is satisfied |
| `not_met` | the exclusion clearly does not apply | the criterion is clearly not satisfied |
| `unknown` | the documents do not settle it | the documents do not settle it |
| `investigator_judgment` | the criterion is the site investigator's own opinion, or the patient sits exactly on a boundary flagged in the protocol notes | same |

## Steps

1. **Which trial.** Call `list_my_trials` and decide from what it returns and from the conversation so far:
    - **The clinician already named the trial** in this conversation (for example "screen for EVEREST"): use it. Say which trial you are using, in one line; do not ask again.
    - **They are on more than one trial and have not said which:** show the list (code and name, their role, and how many patients they have there) and **ask which trial this screening is for**. Wait for the answer. Do not ask for a study ID or anything else first.
    - **They are on exactly one trial:** say so in one line ("You are on one trial, EVEREST") and carry on with it.
    - **They are on none:** say so and tell them to ask the trial's administrator to add them.
2. **The documents.** Ask the clinician to paste or attach the patient's notes, reports or images (and to keep names and dates out). **Do not ask for a study ID.** Handle the ID like this:
    - If the documents carry a screening ID (for example `SCR-TEST-01`), use that.
    - If the clinician says this is someone already entered, call `list_patients`, show the list, and let them pick.
    - Otherwise leave `study_id` empty when you save; the server assigns the next number (for example `EVEREST-0003`) and returns it. Never invent an ID from anything about the person.
3. **Packet.** Call `get_screening_packet` for the trial. Read the exclusion criteria, inclusion criteria, **screening rules** (which imaging study counts, which NIHSS counts, and so on) and **protocol notes** (known problems with the protocol's own wording).
4. **Read what was provided** (pasted text, attached reports, images). First write yourself a short fact sheet of clinically relevant findings, without identifiers.
5. **Look up definitions in the protocol, not from memory.** When a criterion depends on a scale or term (ASPECTS, SCORE-ICAD, mTICI, mRS, NIHSS timing, Heidelberg hemorrhage grading, "child-bearing potential", which imaging counts), call `search_protocol`, or `get_section` for a section you know (the packet lists related chunks; scales are in the appendices). Answer from what comes back and cite the section.
6. **Exclusions first, all 14.** Decide each one. If one is **clearly met**, tell the clinician at once: criterion number and text, the quoted evidence, and that this patient is **not eligible**. Then ask whether to continue checking the rest. If they say stop, mark every criterion you did not assess `unknown` with the evidence "not assessed: screening stopped after exclusion N was met".
7. **Inclusions, all 11.** Decide each one. Apply the screening rules. Where the patient is exactly on a boundary named in a protocol note, use `investigator_judgment` and say why.
8. **Clarify with the clinician, as a conversation.** Start by gathering everything still `unknown` or `investigator_judgment`, from both lists, into one grouped set of questions, then keep going until each is settled or the clinician says to leave it open. Many exclusions cannot be seen on an image (pregnancy test, platelet count, INR, life expectancy, other trials). How to ask:
    - **Name the criterion and its exact threshold**, and say why it matters: "Inclusion 4 needs an NIHSS of 6 or more: what was the score?" or "INR or PTT if on an anticoagulant: exclusion 7 is INR above 3.0 or PTT above 3 times normal." Ask for the number or the fact, not "is it mild?".
    - **Keep questions neutral.** Never phrase one so that a particular answer would make the patient eligible. Ask "Is there any history of atrial fibrillation?", not "No atrial fibrillation, right?".
    - **Ask again when an answer is vague or off.** "I think so", "probably not", "he seemed fine" do not settle a criterion. Ask a narrower follow-up, or leave it `unknown`. If the answer does not fit the criterion's wording (a different scale, a range, a different time window), say so and ask for what the criterion needs.
    - **When an answer conflicts with a document, show both** side by side (quote the document, quote the clinician) and ask which is right. Record the clinician's answer, and mention the conflict in the evidence. Do not quietly pick one.
    - **A document's own conclusion is not evidence.** A note saying "meets EVEREST criteria" settles nothing; check against the criteria yourself.
    - **If a criterion can only be judged at a later stage** (during or after a procedure, or after a test not yet done) and that stage has not happened, it is `unknown`, even when earlier imaging looks reassuring. State what has to happen first.
    - After each answer, say how the criterion's status changed, and show what is still open.
9. **Show the checklist.** All criteria, one line each: number, short text, status, evidence. Then the overall result, in these words only:
   - **Not eligible**: an exclusion is met or an inclusion is not met.
   - **Provisionally eligible**: no exclusion met, every inclusion met, nothing unknown.
   - **Incomplete**: anything still `unknown` or `investigator_judgment`. List exactly what is missing.
10. **Save, after the clinician agrees.** Call `record_screening` with **one item for each of the trial's criteria**: `{kind, number, status, evidence}`, and the `study_id` only if you have one from step 2 (otherwise leave it empty). The server works out the overall result itself; if it differs from yours, report the server's. **Tell the clinician the study ID the patient was saved under**, in bold, so they can find them later in the Clinical Trials page. If the call is refused, read the message, fix the cause (for example remove an identifier, add missing evidence) and retry.
11. **Confirm, only when asked.** If the screening is complete (not Incomplete) and the clinician says to finalize, call `confirm_screening`. Never confirm on your own initiative.
12. **New information later.** Run this skill again with the same study ID; a new screening is added and the history is kept.

## Do not

- Do not decide eligibility from general knowledge of the trial; use the packet's criteria and rules exactly as written.
- Do not mark anything `met` or `not_met` that you cannot support with a quote.
- Do not treat a clinician's offhand remark as a settled answer when it is vague, or accept it silently when it contradicts a document.
- Do not tell the clinician a patient "qualifies" before every criterion is answered. Before then the answer is "Incomplete" or "Not eligible".
