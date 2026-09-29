# Evidence guide: where proof lives in a reproduction package

## Environment

**Where it lives:**
- Eval bundle: the environment record inside the repro report. Read it against the OS and version the issue context states and the repo-facts block (which lists what the repo's bug-report template asks for).
- Live mode: the environment section of the student's repro draft. Compare it to the issue thread and the repo's README or setup docs.

**What good looks like:** It names the OS, the app or runtime version tested, and any other version info the template asks for (for example desktop version info). Those match what the issue targets, or the difference is called out explicitly. A stranger could rebuild the same setup without asking a question. "Latest", "my machine", or a missing version does not count.

## Steps

**Where it lives:**
- Eval bundle: the steps in the repro report, read against the trigger the issue context describes. The repo-facts block gives the bug template's asks.
- Live mode: the steps in the draft, checked against the issue and the repo's setup docs.

**What good looks like:** A stranger can start from the stated starting state and follow the steps in order to the trigger without guessing anything. Where the repo's docs give setup commands, the steps use them. It judges whether the steps would work, not how many there are or what headings they use.

## Behavior shown

**Where it lives:**
- Eval bundle: the artifacts in the repro report (output excerpts, logs, screenshots), read against the symptom in the issue context.
- Live mode: the pasted output or screenshots in the draft, read against the issue's description.

**What good looks like:** The artifact shows the same symptom the issue describes: the same error text or the same wrong result. An adjacent failure does not count. That includes a different error, a failure on another feature, a setup problem, or output that only looks similar. If the artifact shows nothing observed, the behavior is not shown.

## Honesty

**Where it lives:**
- Eval bundle: the outcome the report states ("reproduced" or "cannot reproduce"), set beside the artifacts that back it.
- Live mode: the conclusion line of the draft against its pasted evidence.

**What good looks like:** The stated outcome is exactly what the artifacts support. A report that says it could not reproduce, and shows what it ran and what happened, is honest and passes. A report that claims "reproduced" when the artifact shows a different behavior, or claims more than the evidence shows, does not.

## Comms

**Where it lives:**
- Eval bundle: the claim comment and the repro comment, read against the issue context and the repo-facts block (contribution guide, comment templates, AI-use policy).
- Live mode: the draft comments against the issue thread and the repo's CONTRIBUTING file or policy pages.

**What good looks like:**
- The claim names the specific issue and promises only an investigation and a report. It promises no fix and no date.
- The comments follow any convention the repo states.
- If the repo requires disclosure of AI assistance, the comments disclose it. A comment that omits a required disclosure fails, however good the rest is.
- The absence of a disclosure statement is a fail when disclosure is required; never assume no AI was used.
- Specific, honest wording beats boilerplate ("I'll look into this!") that could be posted on any issue.
