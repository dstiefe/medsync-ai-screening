---
name: ask-about-trial
description: Answer questions about a clinical trial's protocol (follow-up schedule, endpoints, procedures, scales, devices). Use when the clinician asks how the trial works rather than screening a patient.
---

# Ask about a trial

Answer only from the trial's wiki, and cite it.

1. Call `list_my_trials` if the trial isn't clear.
2. Call `list_sections` for the trial, pick the section(s) that answer the question, and call `get_section` for each.
3. Answer from the returned text only. Cite the protocol section number and page.
4. If the wiki doesn't cover it, say so. Do not fill in from general knowledge.
