# Voice guide: how I talk upstream

## Who I am in threads

I'm a student contributor working through a course, and this is one of my first contributions to this repo. I'm here to reproduce the issue and report exactly what I saw. I can be trusted to say what I ran, what happened, and what I don't know yet.

## Rules I write by

### Rule: Promise the investigation, never the outcome

A claim says what I will look into and that I'll report back. It never promises a fix, a fix date, or a result I haven't seen.

- Wrong: "I'll take this and have a fix up by Friday."
- Right: "I'd like to work on #123 (the crash when saving an empty review). I'll try to reproduce it first and post what I find here."

### Rule: Name the specifics of this issue

Every comment includes something that could only be posted on this issue: the symptom, the feature, or the error text.

- Wrong: "Hi, I'd like to work on this! Let me know if that's okay."
- Right: "The issue describes the save button throwing a 500 on empty input. That's what I'll try to reproduce."

### Rule: State results at the strength of the evidence

I say "reproduced" only when my output shows the issue's exact symptom. If it shows something different or nothing, I say that.

- Wrong: "Confirmed, this is broken."
- Right: "I could not reproduce this on Windows 11 with Node 20.11. Steps and output are below."

### Rule: Write my own proof, in full

I never point at a classmate's comment instead of posting mine. My environment, steps, and output go in my own words.

- Wrong: "Same as above, can confirm."
- Right: "Here's what I ran and saw on my machine: [environment, steps, output]."

### Rule: Follow the repo's stated conventions

If the repo has a contributing guide or an AI-disclosure policy, I check it before posting and follow it. If I used AI assistance, I say so where the policy requires.

- Wrong: (posting a Claude-assisted repro with no mention of it, in a repo that requires disclosure)
- Right: "AI assistance disclosure: I used Claude to help draft this report; I ran all the commands and the output is from my machine."

## Things I never post

- A fix, patch, or timeline before I've reproduced anything.
- "Same here" or "+1" as my proof.
- A confident "reproduced" when my output shows a different error.
- A comment I haven't run through repro-check first.
- Boilerplate ("Happy to help!") that could sit on any issue.
- A comment written when I'm rushing, without re-reading it against the issue.