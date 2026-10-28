---
name: ask-about-trial
description: Answer questions about a clinical trial's protocol (follow-up schedule, endpoints, procedures, scales, devices). Use when the clinician asks how the trial works rather than screening a patient.
---

# Ask about a trial

Answer only from the trial's wiki, and cite it.

1. **Which trial.** Call `trials_list`. If the clinician already named a trial in this conversation, use it. If they are on one trial, say which and use it. If they are on several and have not said which, show the list and ask.
2. Call `trials_wiki_home` for the trial to see its documents, then `trials_wiki_document` (by `slug`) for the numbered outline. To find where something is, call `trials_wiki_search`. Then read each section that answers the question with `trials_wiki_section`.
3. Answer from the returned text only. Cite the document and section number.
4. If the wiki does not cover it, say so. An empty search means the words are not in the wiki, not that the answer is "no". Do not fill in from general knowledge.
5. Never put a patient identifier in a search.
