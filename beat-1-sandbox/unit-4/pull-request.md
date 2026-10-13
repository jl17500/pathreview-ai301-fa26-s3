# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/191

**Branch**

fix/18-repo-file-structure

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

I did one calibration smoke test and one full scored evaluation. There were no partial scored reruns and no second full evaluation. The live checks I ran on my own pull request (`pr-precheck` on PR #191's package) are separate from these evaluation runs and are not counted here.

1. **Calibration smoke test** (`smoke-calib-01.json`): `calib-01` only. My tool accepted it, matching its gold label (`accept`). Calibration packages are never scored, so the file records agreement `[0, 0]` and this run has no score.
2. **Full evaluation** (`full-run-1.json`, saved as `eval-run.txt`): **19/20** scored packages agreed with the gold labels (bar: 18/20, PASS). The per-category line in `eval-run.txt` reads `clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. The only disagreement is `pkg-08`, in the clear-accept category. `eval-run.txt` is the harness-written file from this run; I did not edit it, and it lists the final score above.

The fingerprints in the `eval-run.txt` header (`SKILL.md`, `rubric.md`, `procedure.md`, `evidence-guide.md`) match the files I uploaded to `tools/pr-precheck/`.

One compatibility edit was needed to launch the harness on Windows: `run_eval.py` now has `import shutil` and starts the grader with `shutil.which("claude") or "claude"`. No grading logic, model, or labels changed.

**Package analysis**

**Package: `pkg-08`** (jesseduffield/lazygit#5883, "Show an error instead of the stash prompt when only untracked files exist").

**What my tool decided:** reject. It graded `plan-fidelity`, `repro-evidence`, `diff-reviewable` and `repo-standards` as pass, and failed `description-accurate` and `repo-checks`. **What gold said:** accept (category `clear-accept`). So this is the one disagreement in the run.

**Why my rubric rejected it.** The repository's PR template, as the package records it, asks for "a requirements checklist: cheatsheets up to date (go generate), code formatted, tests added or updated (integration test guide linked), text internationalised, new UserConfig entries hot-reloadable and documented". The description answers that checklist with: "Requirements: cheatsheets regenerated (no binding changes, no diff); code formatted; integration test added; new error text internationalised; no UserConfig changes." The test evidence supports some of these: "Integration test `stash_untracked_only_errors` passes; `go test ./...` passes; `go generate ./...` produces no cheatsheet diff." It records no formatter command, no attempt, and no outcome for "code formatted". My tool graded that as a claim of a passing check without a recorded result. Its `repo-checks` evidence reads "The template requires formatted code, but no format command, attempt or outcome is recorded; go generate and go test ./... are recorded, but the formatting check is missing", and its `description-accurate` evidence reads "Description claims 'code formatted' but the test evidence records no formatter command or outcome". That follows the rubric as written: a required check needs an identifiable command, an actual attempt, and a recorded outcome, so "code formatted" with nothing behind it is unsupported. The gold label accepted the package, so my stricter reading is the disagreement.

**A second, separate reading by the grader was mistaken.** The `description-accurate` evidence line continues: "'plan posted on the thread' is also not visible in the thread highlights." The description says "(#5883, plan posted on the thread)". The package's thread section is only "Thread highlights (2 comments total)", two short comments (one hopes for a fix, one says they pushed a fix). The package's own Plan context says: "The accepted plan this PR claims to implement, excerpted from the posted plan (with the repro evidence it built on)". That statement identifies the plan as posted, so a plan not appearing in the highlights does not show it was never posted, and it does not contradict the description. This was a misreading by the grader, not a rubric requirement. The missing formatter evidence is the actual, stricter evidence rule at work. The thread inference is not, and I do not count it as a valid reason to reject `pkg-08`.

**Check rationale**

The check I am quoting is `repo-checks`, copied exactly from the `rubric.md` I uploaded to `tools/pr-precheck/`:

```text
| repo-checks | The commands, attempts, and actual outcomes recorded in the test evidence for each check the repository requires and each relevant check in the plan's test plan (live: also the CONTRIBUTING and PR template asks; eval: the repo facts); see Test evidence (repository checks) | Pass if each required or planned relevant check has an identifiable command, an actual attempt, and a recorded outcome (passed, failed, or blocked), and none is misreported. A check recorded as skipped or not run does not pass on a stated reason alone: it passes only when concrete evidence establishes why execution is impossible (for example quoted error output from an attempt) or why the check does not apply (for example repo facts showing it does not cover the changed files). Lack of time, inconvenience, or an assumption that another test covers it are not valid exceptions. An attempted check with an evidenced environment blocker passes this check when reported accurately; whether an explicit repository prohibition or requirement still bars the PR is decided under repo-standards, not here. A failure passes only when shown to be unrelated to the change (for example, the same failure recorded on the base branch). Local checks that should already have been attempted are graded as above; CI that cannot run until the PR is opened is pending, must be described as pending, and is never counted as passed. In live mode on Path Review the required checks are `make test-unit`, `make test-integration`, `make lint`, and `make typecheck`. Do not require a full test suite unless the repo facts or plan state one. Fail if a required or planned check has no command, attempt, or outcome ("tests pass" alone), failed because of the change, was skipped or not run without concrete evidence of impossibility or inapplicability, or is reported as passed without a recorded result. | required |
```

**Why it reads that way.**
- **Identifiable commands, attempts and outcomes.** The row requires that each required or planned check has "an identifiable command, an actual attempt, and a recorded outcome (passed, failed, or blocked)". Without all three, a line like "tests pass" cannot be told apart from a claim nobody ran, and a pull request that lists checks can look ready when it is not.
- **Evidenced blockers and baseline failures are allowed.** An attempted check "with an evidenced environment blocker passes this check when reported accurately", and "A failure passes only when shown to be unrelated to the change (for example, the same failure recorded on the base branch)". A genuine blocker or a failure that also happens on the base branch should not sink an honest package.
- **Pending CI is kept separate.** "CI that cannot run until the PR is opened is pending, must be described as pending, and is never counted as passed." Local checks should already have been run, while CI that cannot yet run is pending and never counts as a completed local check.
- **A reason alone is not enough.** "A check recorded as skipped or not run does not pass on a stated reason alone": it needs concrete evidence such as quoted error output, or repo facts showing the check does not cover the changed files. The row also says "Lack of time, inconvenience, or an assumption that another test covers it are not valid exceptions", and I rejected those as grounds for skipping a required check.

These clarifications were made to the rubric before the saved full evaluation, so the `eval-run.txt` I am submitting was graded against this row as it reads now.

**Trade-offs**

Requiring recorded check evidence is what lets this check catch unsupported "tests pass" claims: a package that says its checks passed must show the command and the outcome. The cost is that it can reject a package that is otherwise acceptable, and `pkg-08` shows exactly that. It has a recorded, working fix with before/after output, a control, an integration test, `go test ./...` and `go generate ./...`, and the gold label accepts it. My tool rejected it because "code formatted" was asserted in the description with no formatter command or outcome recorded. That is the price of the stricter rule: a package can be sound while its write-up leaves out one checklist item's evidence, and my tool will fail it for that.

That cost is a deliberate trade-off. The grader's separate inference that the plan was "not visible in the thread highlights" is not one: it was a misreading, because the package's Plan context says the plan was excerpted from the posted plan, and I do not accept it as a desirable side effect of the rule.

I kept the skill as it was after the 19/20 result and documented the `pkg-08` disagreement here instead of changing the rubric. I did not revise the skill after the saved evaluation, and I did not run a canary with `--only` or any later evaluation.


---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
