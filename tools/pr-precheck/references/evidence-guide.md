# Evidence guide: where evidence lives in a PR package

Four families, one per harness failure category. Every rubric check points at a family here; each check has its own labeled entry (Check: name) so the mapping is one to one:

| Family | Harness category | Rubric checks |
|---|---|---|
| Plan fidelity | silent-drift | plan-fidelity, description-accurate |
| Test evidence | not-tested | repro-evidence, repo-checks |
| Diff quality | unreviewable | diff-reviewable |
| Standards and comms | standards-wall | repo-standards |

**Eval bundle layout** (use only these sections in eval mode): Repo facts (PR template asks, contribution policy, AI policy), Issue, Thread highlights, Plan context (scope, files, not-in-scope, test plan, the original repro evidence, and any deviation notes), and the Candidate PR (Title, Description, Commits, Diff, Test evidence).

**Live working copy**: `plan.md` (with its deviation notes), `pr_draft.md` (title and description), `test_evidence.md`, the branch's committed diff from `git diff main...HEAD`, the commit list from `git log main..HEAD`, the working-tree status from `git status`, and, on GitHub, the issue thread and linked reproduction, the PR template, and `docs/CONTRIBUTING.md`. A house-chain student uses the house plan and house repro pack in place of their own plan and repro.

## Plan fidelity (harness category: silent-drift)

**Check: plan-fidelity**

Where it lives:
- Eval: Plan context for the plan's scope, files, steps, "not in scope", and any deviation notes; the Candidate PR's Diff for the changed files and hunks; the Description for its account of what was done and left out.
- Live: `plan.md` including its Deviations section; the changed files and hunks in `git diff main...HEAD`; the description in `pr_draft.md`.

What good looks like: sort the plan's steps into implementation changes (edits to code, tests, docs, config) and verification or investigation steps (run, check, confirm). Implementation changes are read against the diff: every behavior change in the diff maps to a planned implementation change or a recorded deviation, and every planned implementation change shows up in the diff or in a deviation that the description repeats accurately. Supporting edits the planned change needs (its tests, an updated call site) map to the plan by that need. Verification and investigation steps are read against their recorded evidence in the test evidence, not against the diff; they leave no hunks, so their absence from the diff is not omitted work. A deviation recorded in the plan re-ties a mismatch, including a change to the original scope (work the plan first listed as out of scope, or planned work dropped); a deviation that exists only in the diff, or only in the description, does not. Silent drift has two directions: extra work (hunks the plan and deviations do not cover) and omitted work (planned implementation absent from the diff while the description says nothing or implies it was done). A narrower result is fine when the deviation and description explain it accurately. Work that is unrelated to the issue is read under Diff quality.

**Check: description-accurate**

Where it lives:
- Eval: the candidate PR's Title and Description, compared with the Diff, the Issue section (for the issue number and symptom), and the Test evidence (for any result the description states).
- Live: the title and description in `pr_draft.md`, compared with `git diff main...HEAD`, the real issue thread, and `test_evidence.md`.

What good looks like: the title states plainly what changed; the description gives the correct issue reference, says what changed and why in a way the diff bears out, and states results no stronger than the recorded evidence (a "fixes" or "all checks pass" claim has recorded outcomes behind it; a known limitation or remaining failure is stated). A description that claims more or less than the diff delivers is silent drift, not a style problem. Pending CI that has not run is written as pending, never as passed. Length and polish do not matter: compare each claim against the diff and the results.

## Test evidence (harness category: not-tested)

**Check: repro-evidence**

Where it lives:
- Eval: the candidate PR's Test evidence, compared with the original repro evidence and the test plan in Plan context, and with the symptom in the Issue section.
- Live: `test_evidence.md` (commands and captured output), compared with the issue's linked reproduction (or the house repro pack) and the test plan in `plan.md`. Provenance: results captured before the commit, while the changes were still uncommitted, are usable when the tested code matches the submitted code. Compare what was tested (the files and state the output came from) with `git diff main...HEAD`, and note any actual difference and whether it touches the behavior tested; do not discount evidence only because it predates the commit.

What good looks like: decisive evidence names the command or manual steps, the trigger that reproduces the issue, the observed before-result, the expected after-result, and the observed after-result, so the output differs between the original failure and the claimed improvement. A manual check with precise steps and observed output counts. Mocked or stubbed tests are marked as such and support only what the mock exercises; live-behavior claims need a live observation. A disclosed partial result shows exactly what improved and what remains, and the plan's deviations and the description record that limit; the evidence supports the improvement it shows and nothing beyond it. Disclosure does not stand in for running the relevant checks. "Tested and works" with no commands or output is not evidence.

**Check: repo-checks**

Where it lives:
- Eval: Repo facts for the checks the repository requires (and its stated commands), Plan context's test plan for planned relevant checks, and the Test evidence for the command and outcome of each.
- Live: `docs/CONTRIBUTING.md` and the PR template for required checks (on Path Review: `make test-unit`, `make test-integration`, `make lint`, `make typecheck`), `plan.md`'s test plan for planned checks, and `test_evidence.md` for commands and outcomes.

What good looks like: a table or list that shows, per required or planned check, the exact command, that it was attempted, and its actual outcome: passed, failed, blocked, skipped, or not run. A blocked check shows the attempt and the evidence of the blocker (quoted error output, an environment limit, a pre-existing failure shown on the base branch). A skipped or not-run check needs concrete evidence that execution is impossible or that the check does not apply; a reason such as lack of time, inconvenience, or "another test covers it" is not evidence of either. No result is rewritten as passed in the description. A check that was attempted and blocked is different from one that never appears. Whether an explicit repository requirement or prohibition still bars the PR is read under Standards and comms. Do not require a full suite the bundle does not state. Local checks should already have been attempted before the PR is opened; only CI that cannot run until then is "pending", and it is never "passed". This tool checks readiness to open a PR, not readiness to merge.

## Diff quality (harness category: unreviewable)

**Check: diff-reviewable**

Where it lives:
- Eval: the candidate PR's Diff (every hunk and every file path in it) and its Commits list.
- Live: `git diff main...HEAD` for the committed change, `git log main..HEAD` for the commits, and `git status` for what is uncommitted or untracked (uncommitted work does not ship; report it, and treat accidental draft files that are committed as part of the diff).

What good looks like: the fix is visible, the supporting tests sit beside it, and nothing unrelated rides along; a reviewer can say what each hunk is for. The tells: debug leftovers (print or console statements, temporary logging, debugger calls), dead code, commented-out blocks, formatting or whitespace churn outside the touched lines, drive-by refactors or renames, unrelated files, and accidental draft or scratch files (plan, PR draft, notes, logs, backups, local config). Never judge by size: a large, coherent change can be reviewable, and a three-line diff with a stray debug line is not.

## Standards and comms (harness category: standards-wall)

**Check: repo-standards**

Where it lives:
- Eval: Repo facts for the PR template asks, contribution policy, and AI-use policy; Thread highlights for explicit maintainer directions; the candidate PR's Description for how each is honored. If the bundle states no template or policy, nothing applies from that source.
- Live: the repo's PR template and `docs/CONTRIBUTING.md` (fetch them; report inaccessibility rather than assuming none), the issue thread for maintainer directions, `scope.md` for the house rules (own-fork branch, one PR per issue, template always used), and the draft description in `pr_draft.md`.

What good looks like: every template section that applies is filled with real content, or carries a meaningful N/A (for example, "no UI change, so no screenshots"); contributor-policy asks (such as referencing the issue or keeping the PR small) are met; an AI-use disclosure appears wherever a policy requires one, and for a course PR it is in Notes for Reviewers and specific (which tool, for what, and what the author checked by hand); explicit maintainer directions in the thread are engaged, not ignored. Boilerplate, blank sections, deleted sections, and a visibly ignored ask are the tells. A documented deviation explains a departure from a preference but does not waive an explicit prohibition. Whether the description's claims match the diff is plan fidelity, above; this check reads only the repo's stated asks.
