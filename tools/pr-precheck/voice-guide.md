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

## Rules for pull requests

These add to the rules above; they apply to the PR title and description.

### Rule: A factual title

The title says what the change does to this code, in plain words. It never promises more than the diff does, and it carries no hype.

- Wrong: "Fix everything wrong with theme parsing"
- Right: "Remove duplicate file-decoration-style from gruvmax-fang theme"

### Rule: A concise explanation that names the issue

The description says what changed and why in a few sentences, references the issue by number, and uses words only this change could have. It is not a tour of every line.

- Wrong: "This PR improves the codebase and resolves the problem."
- Right: "Removes the shadowed first `file-decoration-style` line from the gruvmax-fang theme (#2211) so strict parsers no longer raise a duplicate-key error."

### Rule: Disclose AI use specifically

If a repo or course asks for AI disclosure, I say which tool I used, what it helped with, and what I checked myself. For a course PR it goes in Notes for Reviewers. A vague line does not count.

- Wrong: "AI was used."
- Right: "AI assistance disclosure: I used Claude to help draft the description and review my diff. I made the code change and ran every command in the test evidence myself."

### Rule: Keep observed, expected, and limits apart

What I ran and saw, what I expect to be true but have not seen, and what I did not or could not do are three separate things, and the description keeps them in separate sentences. I never write an expectation as a result.

- Wrong: "Tests pass and CI is green."
- Right: "Observed: `make lint` passed on my branch (output in test_evidence.md). Not yet run: CI, since the PR is not open. Limitation: I could not run `make test-integration` because the database service would not start (error quoted in test_evidence.md)."

### Rule: Claim a fix only when I have seen it

I write "fixes" or "resolves" only when my after-output shows the issue's symptom gone. If the failure is still there or only partly fixed, I say so and say what remains.

- Wrong: "Fixes #123." (when the after-run still fails)
- Right: "Addresses part of #123: the empty-input crash is gone, but the timeout described in the issue still reproduces. Output below."

## Things I never post

- A PR title or description I haven't run through pr-precheck first.
- A claim that checks pass when I have not seen the output, including CI that has not run.
- An AI disclosure that doesn't say which tool and for what.
- A fix, patch, or timeline before I've reproduced anything.
- "Same here" or "+1" as my proof.
- A confident "reproduced" when my output shows a different error.
- A comment I haven't run through repro-check first.
- Boilerplate ("Happy to help!") that could sit on any issue.
- A comment written when I'm rushing, without re-reading it against the issue.