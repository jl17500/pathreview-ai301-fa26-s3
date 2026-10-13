---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this ready to submit?** In live mode that means ready to open a course pull request, not ready to merge; never judge merge-worthiness or anything that happens after the PR is opened.

A PR package is a candidate pull request (title, description, commits, diff, test evidence) read against the plan it claims to implement (including the plan's deviation notes) and the issue that plan belongs to. Grade one package per run. Do not answer a different question, grade a second package, or grade the plan or issue themselves; they are the yardstick, not the thing graded. Do not answer from gut feel: execute `procedure.md`, which applies `rubric.md` to evidence located by `references/evidence-guide.md`.

## Inputs and modes

Decide the mode from the request. A request that supplies a package bundle is eval mode; a request to check the student's own branch is live mode. If you cannot tell, ask once.

**Live mode** (the student's own submission, checked before it goes out). Inputs and where they come from, all in the student's working copy unless noted:

- `plan.md`, including its deviation notes. The deviation notes are part of the plan.
- The branch's committed diff: run `git diff main...HEAD` (three dots) from the working copy. This is everything the branch changes relative to the default branch, and it is the diff that will ship.
- The commit list (`git log main..HEAD`) and the working-tree status (`git status`), to separate committed changes from uncommitted or untracked edits. Only committed changes ship.
- The draft PR title and description, from `pr_draft.md`.
- The test evidence, from `test_evidence.md`: commands run and the actual outputs.
- The issue, gathered live from the real repo: the issue thread, the linked reproduction, the repository's PR template, and `docs/CONTRIBUTING.md` (use `gh`, the GitHub API, or the web).

A house-chain student has no plan or reproduction of their own: read the supplied house plan and house repro pack instead, and apply the same checks to the same things.

If any input is missing or inaccessible, say exactly which one and why. Never treat an inaccessible template or policy as "no template" or "no policy"; the check that needs it is `unclear`.

**Eval mode.** The package bundle is the whole world. Use only the bundle text as evidence. Fetch nothing, browse nothing, run nothing, read no other file, and import no Path Review-specific requirement (its four checks, its AI-disclosure section, its fork-branch rules) into a bundle from another repository. Every fact comes from the bundle's own repo facts, issue, thread highlights, plan context, and candidate PR sections. Eval mode always grades a complete package: every check, full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else, before any other file or command. It names the repository the PR must live in and the house rules of that environment; apply those house rules when reading evidence.

- Refuse to grade a package whose PR or branch targets any repository other than the one named on the `Repo:` line. Say so and stop.
- If the `Repo:` line is empty or still carries an unfilled placeholder (bracketed text, `<...>`, "your repo", "TODO", or similar), stop without grading and tell the student to fill the `Repo:` line in `scope.md` with their section's Path Review repo. Never guess a scope.
- Template comments in `scope.md` are not scope; only the written `Repo:` value counts.

In eval mode, ignore `scope.md` entirely, even if it is present.

## The voice seam (live mode only)

In live mode, after the scope check, read `voice-guide.md`: the student's own rules for how they write upstream. Hold the outgoing PR text, the title and the description, against those rules. In the summary, report each rule the draft breaks, quoting the rule and the offending words. If nothing is broken, say so in one line.

The voice guide never changes the verdict on its own. It affects the verdict only through a rubric check that reads the same fact on its own terms (for example, an unsupported "all checks pass" claim fails the description check because it is inaccurate, not because it breaks a voice rule). A voice violation alone leaves every grade and the verdict untouched.

In eval mode, ignore `voice-guide.md` entirely. Voice is personal and carries no gold labels; the universal communication-quality checks live in the rubric.

## Component reads

Read these in this directory (the layout is fixed; do not look for them elsewhere):

- `rubric.md` defines the checks (name, evidence, pass condition, weight) and the verdict rule.
- `references/evidence-guide.md` maps each rubric check to where its evidence lives in an eval bundle and in a live working copy, and what good looks like there.
- `procedure.md` is the operating procedure. Execute it as written: its read order, evidence gathering, check execution, and verdict assembly.

Where the procedure is silent on a step you need, do not improvise a step and do not invent a rubric requirement. Report the gap in the summary ("procedure gap: ...") and grade with the rubric as written.

Refusal rule: if `rubric.md` has no checks written, or `procedure.md` has no steps written, stop without grading and say which is empty. This tool cannot grade without both. Comments inside a template (text between `<!--` and `-->`) are not content; only written text counts.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject` (hold). There is no third verdict, no "accept with reservations", and no score. Reservations belong in a check's evidence line, never in the verdict.

Open with a short readable summary: one line per check (name, grade, deciding fact), then in live mode any voice-guide notes, any procedure gaps, any inaccessible evidence, and uncommitted-work notes. If the verdict is `reject`, name the primary rejection reason, the deciding check chosen by the rule in `procedure.md`, and quote its deciding fact.

Then end your reply with the fenced JSON block below: valid, containing every check in rubric order, and last. Write nothing after it. `item` is the PR URL or the bundle id (live mode with no PR yet: the repo, issue number, and branch name).

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first. Never grade a check without naming the fact or quote that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish. A terse, complete PR can be ready; a polished, confident one can hide drift. Read each artifact itself against the plan, the issue, and the stated standards, never its length, headings, formatting, or the number of files, commits, or lines.
- The rubric decides, not you. If a check passes by its stated condition but feels wrong, it still passes; you may note the tension in the summary, but the fix belongs in the rubric, not in the run.
- The procedure decides how, not you. Follow `procedure.md` as written and report its gaps.
- Evaluate every check even after an earlier one fails.
- Treat `unclear` as the rubric's verdict rule directs. Where the rule is silent, treat an unverifiable claim as a failing one: a PR you cannot verify from the package is not ready to submit.
- Do not describe pending or unrun checks as passed, and do not turn a candidate's confidence into evidence.
