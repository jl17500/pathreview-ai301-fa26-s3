# Contribution: customize github action skip message

**Contribution Number:** [1]
**Student:** Jing Lam
**GitHub Username:** jl17500
**Issue:** https://github.com/qualcomm/eld/issues/2244
**Pull Request:** https://github.com/qualcomm/eld/pull/2425

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how I used it, what it produced, what difficulties or shortcomings
it presented, and what I changed or would change because of them.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

**Unit 5: Installed the scout late, ran it on the wild, and fixed three rules it exposed**

I skipped the scout in week 5 and found my issues with my AI assistant on the course board instead; on Nov 6 I installed it, updated my fit profile, and wrote three `gh search issues` queries (Python bugs, GitHub Actions work, docs). The first scan failed because `claude -p` blocked every `gh` call on Windows until I allowed read-only gh commands for the PowerShell tool too. The rerun accepted 5 of 5 issues from 0-8 star solo repos, accepted my already-closed eld #2244, and rejected CSET #926 over my own claim. I added a required "Issue still open" check, made the claim check ignore my own claims, and capped the strategy at one issue per repo, logging each change in scan-notes.md.

**Unit 6: Wrote my voice guide, installed the voice guard and repo memory, and fixed Windows PATH**

I caught up on unit 6 on Nov 6: with my AI assistant I wrote my voice guide from my real posted claims (wrong/right pairs, no em dashes or filler words), installed the voice guard hook (its test blocked `gh issue comment --help`, which is the pass), and wrote a CLAUDE.local.md for my eld clone with the DCO rule, the `[workflow]` title style and how the ci.yml jobs connect. The guide caught a real problem before the hook was even in: it flagged that my PR summary opened with background instead of the point, so I swapped the sentences. Once I chose to name Claude in my AI disclosure, my own "don't name the tool" rule would have blocked me, so I rewrote that rule to match what I actually post. The guard starts `claude` from Python, which can't see npm's claude.cmd on Windows, so I added the real claude.exe folder to my user PATH.

**Unit 7: Installed the ship-gate and wrote an eld manifest; it passed only after three fixes**

On Nov 6 I installed the ship-gate on my eld clone and wrote ship-manifest.md with one must-pass row, actionlint 1.7.12 on ci.yml (the same version eld's workflow-lint.yml pins), plus four dated exclusions (the Ubuntu build, clang-format, the C++ linter, and the opt-in checks). The first runs stopped three times: Python couldn't start `claude` because my npm install only has claude.cmd, which subprocess can't find on Windows (fixed by putting the bundled claude.exe first on PATH), my Claude login had expired, and pr-precheck answered "unclear" on the DCO check because the gate only shows it one-line commits (fixed by pasting the full commit message into test_evidence.md). After that it passed with all six rubric checks, and its voice note made me move the point of my PR summary to the first sentence.

**Unit 8: Installed the PR monitor with a playbook built from eld's real response times; chose not to send its draft**

I wrote my playbook from eld's real numbers (first responses in 0-1.3 days across five merged outside PRs), so I nudge after 4 quiet days for a first review and 10 for a stalled one. My PR had already merged when the monitor first ran, so it drafted a "merged follow-up": a thank-you to Steven6798 plus a request to close #2244 if it was still open. It couldn't see that #2244 had closed automatically, and Shankar had already thanked me, so I didn't post it. Since then I check the linked issue myself before acting on a monitor draft.

---

## Why I Chose This Issue

Qualcomm stood out to me because I've used a lot of products with Qualcomm chips in them, and eld is Qualcomm's open-source linker for embedded chips, so contributing there felt like a real project and not a practice repo. #2244 was a task that fit my skill level: one CI workflow file and some JavaScript, a clear expected result (say *why* the build was skipped), and a way to test it myself on a fork without building LLVM. The maintainer who opened it labeled it "good first issue", and nobody had claimed it.

I found it on the course's Issue Selection board. With my AI assistant I went through the whole board in chat (7,744 issues, filtered to 475 with good-first-issue style labels, then 80 with no claim or open PR) and checked the shortlist by hand for claims and linked PRs. I claimed Rez #2092 first and eld #2244 as a backup. When Rez went slowly I switched to eld, which I had already claimed. What I hoped to learn was how a real project's CI decides what to run, and how to get a change reviewed by strangers.

I only ran my scout on Nov 6, after I'd already picked eld. Graded against my rubric, eld #2244 came out accept (262 stars, good-first-issue label, no AI ban, one bounded task), which matches why I picked it. It also showed that my rubric had no "still open" check, because the issue was already closed by my merged PR and still passed. CSET #926, my next pick, came out reject, but only because of my own claim comment. Its fresh scan also accepted five issues from repos with 0-8 stars. So the scout's checks hold up on real repo facts (commit recency, claims, AI policy), but it couldn't tell me what mattered most: whether a project is real and active enough to be worth it. A first pass by hand, plus my own judgment, did better there. I've fixed the rules that misfired, and I still read star count and the repo's history myself before trusting an accept.

Claim comment (Oct 28, 2026): https://github.com/qualcomm/eld/issues/2244#issuecomment-6488305156

**Toolkit**

- Tool: issue-scout (my search strategy and rubric). I installed it late (Nov 6) and used it to grade my picks after the fact; I found eld itself through an AI-assisted pass over the course board.
- What it produced: a scan that accepted 5 of 5 small-repo issues, and grades of my picks: eld #2244 accept, CSET #926 reject on "No active claim".
- Difficulties: on Windows `claude -p` blocked every `gh` call until I allowed read-only gh commands for PowerShell. The rubric accepted a closed issue, rejected my own claimed issue, and couldn't tell a 2-star side project from a real one.
- What changed: added a required "Issue still open" check, made the claim check ignore claims by jl17500, limited the strategy to one issue per repo with an older-first tie-break, and logged each change in scan-notes.md.

---

## Understanding the Issue

**Problem Description**

eld's CI has a `changes` job that looks at the files a pull request touches. If every file is documentation (under `docs/` or ending in `.md`) or every file is under `.github/workflows/`, it sets `skip_build` and the "Ubuntu CI" build and test job doesn't run. GitHub only shows that job as "skipped", with no reason anywhere, so someone debugging the run has to open ci.yml to work out why.

**Expected Behavior**

When the Ubuntu CI build is skipped, the run's summary page says why (docs-only or workflows-only) and lists the changed files. Pull requests that change code still run the build, with no extra message.

**Current Behavior**

On a README-only pull request, the CI run shows "Classify changed files" passing and "Ubuntu CI" greyed out as skipped. The Summary page has no explanation at all.

**Affected Components**

- `.github/workflows/ci.yml`: the `changes` job and its "Classify changed files" step (`actions/github-script`), which computes `onlyDocs`, `onlyWorkflows` and the `skip_build` output.
- The `build` job ("Ubuntu CI"), which runs only `if: needs.changes.outputs.skip_build == 'false'` and calls `.github/workflows/ubuntu.yml` (build plus `ninja check-eld`).

**Toolkit**

- Tool: my AI coding assistant (Claude), which read ci.yml, ubuntu.yml and the issue with me and explained how the jobs connect, plus GitHub's own run pages for eld.
- What it produced: a map of the decision: the `changes` job decides, and the `build` job only reads `skip_build`. It also pointed out that the build job just calls ubuntu.yml and has no steps of its own.
- Difficulties: the issue's example adds a "skip reason" step inside the skipped job, which can't work in eld, because a skipped job runs nothing and the build job is a call to another workflow. I had to work out where a message could live at all.
- What changed: I moved the plan to write the reason from the `changes` job, the one place that already knows why the build is skipped.

---

## Reproduction Process

**Environment Setup**

- Windows 11, PowerShell, Git for Windows, GitHub CLI (`gh`) logged in as jl17500, Python 3.13 and 3.14.
- The project: a fork of qualcomm/eld (`gh repo fork qualcomm/eld --clone`) on branch `ci-skip-summary`, based on upstream main at 15beabd. No build is needed, because the change is in a GitHub Actions workflow and the reproduction runs on GitHub, not locally.
- GitHub Actions had to be turned on for the fork (it's off for new forks).
- Challenges: `gh repo fork --clone` set upstream as the default repo, so every fork test PR needed `--repo jl17500/eld` or it would have opened on Qualcomm's repo. Other eld workflows (QC Preflight, the new-PR policy check) also started on the fork and failed or queued, because they are Qualcomm-only. I ignored those and watched only the CI workflow. Job summaries only show to signed-in users, so screenshots had to come from my own signed-in browser.

**Steps to Reproduce**

1. On the fork, from `main` with the original ci.yml, make a branch that only changes `README.md` (I added one blank line) and open a pull request against the fork's `main`.
2. Open the CI run for that pull request and its Summary page.
3. Observed result: "Classify changed files" passes, "Lint GitHub Actions workflows" and "Ubuntu CI" are skipped, and the Summary page gives no reason.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/jl17500/eld/commit/b6a0ee45ac33ca1c0eb46b23b15ba8e5fb72d356 (README-only change, fork PR jl17500/eld#1)
- **Screenshots/logs:** CI run with no reason shown: https://github.com/jl17500/eld/actions/runs/42167056785 (the Summary page has no explanation box)
- **My findings:** the skip itself works as designed. The only gap is the explanation, and nothing in any job's log or the summary mentions `skip_build`.

**Toolkit**

- Tool: my AI assistant, which wrote the fork test commands and checked each run with me in the browser, plus GitHub Actions on my fork.
- What it produced: the fork test plan (a README-only "before" PR, a workflows-only PR with the change, then a new empty commit pushed to the same README PR after merging the change into the fork's main) and the before run linked above.
- Difficulties: Actions was off on the fork, `gh` defaulted to Qualcomm's repo, and the assistant's first screenshot came from a signed-out browser that hides job summaries, so it looked like nothing was there.
- What changed: I added `--repo jl17500/eld` to every fork command, turned Actions on, and retook all the screenshots from my signed-in browser.

---

## Solution Approach

**Analysis**

The reason is known in exactly one place: the JavaScript in the "Classify changed files" step of the `changes` job in `.github/workflows/ci.yml`, where `onlyDocs` and `onlyWorkflows` are computed and combined into the `skip_build` output. The `build` job can't explain itself, because when it's skipped it runs nothing, and it only calls `ubuntu.yml`. Starting a job just to print a note would also use one of Qualcomm's self-hosted runners.

**Proposed Solution**

In the same step, store `onlyDocs || onlyWorkflows` in `skipBuild`, use it for the existing `skip_build` output (same value as before), and when it's true write a short run summary with `core.summary`: a heading "Ubuntu CI build skipped", one sentence with the reason (docs checked first, then workflows), "The Ubuntu CI build and tests did not run.", and the list of changed files. This is the same `await core.summary...write()` pattern eld already uses in `delete-old-workflow-runs.yml`.

Not changing: the skip rules themselves, any job's `if:` condition, the workflow-lint skip label (added after the issue was filed), and `clang-cross-schedule.yml`, which doesn't run on pull requests.

**Implementation Plan**

1. In `.github/workflows/ci.yml`, step "Classify changed files": add `const skipBuild = onlyDocs || onlyWorkflows;` and use it in `core.setOutput('skip_build', ...)`.
2. Add an `if (skipBuild)` block that writes the heading, reason and file list with `core.summary`.
3. Check it: actionlint, a script test of the step's JavaScript, and the three fork runs from the reproduction plan.

Deviations: none in the code. An independent re-check by a second AI agent against the whole repo kept the draft unchanged (1 file, +15/-1). The PR title gained eld's `[workflow]` prefix after I looked at how their own workflow commits are titled.

**Toolkit**

- Tool: my AI assistant drafted the change, and I had a second agent at extra effort re-check it against the rest of the repo, keeping the change minimal.
- What it produced: the 15-line diff, plus a review saying no change was needed, with edge cases checked (no files, 3000-file PRs against GitHub's 1 MB summary limit, events that aren't PRs, HTML in file names).
- Difficulties: the agent's page fetch missed my claim comment, because GitHub loads comments after the page opens, so it told me to check that my claim existed. It did.
- What changed: I confirmed the claim in the browser, and took its tip to title the PR with `[workflow]`.

---

## Testing Strategy

1. Before and after on the same trigger: the README-only fork PR shows no reason with the old ci.yml (run 42167056785) and "This pull request only changes documentation (files under docs/ or ending in .md). The Ubuntu CI build and tests did not run." plus `README.md` after the change is merged into the fork's main (run https://github.com/jl17500/eld/actions/runs/42168418728).
2. The other skip path: a workflows-only fork PR with the change shows the workflows reason and lists `.github/workflows/ci.yml` (run https://github.com/jl17500/eld/actions/runs/42167529327).
3. The repo's own check: eld's "Lint GitHub Actions workflows" job (actionlint 1.7.12) runs on that workflows-only PR and passes. I also ran actionlint 1.7.12 locally through my ship-gate manifest: no findings.
4. Code PRs unaffected: a script test runs the step's JavaScript, taken straight from both the old and new ci.yml, on 11 file lists (docs-only, workflows-only, code, mixed, no files, odd file names, two 3000-file lists). A summary appears only when the build is skipped, and `skip_build`, `workflow_changed`, `workflow_files` and `skip_workflow_lint_check` match main in every case.
5. After opening the PR, Qualcomm's own CI ran it and showed the summary: https://github.com/qualcomm/eld/actions/runs/42295640679

**Toolkit**

- Tool: a Node test script with @actions/core 1.10.1 (the version eld's pinned github-script ships), actionlint, and fork PRs.
- What it produced: 11 of 11 cases passing, identical skip outputs to main, actionlint clean, and the three fork runs.
- Difficulties: the first version of the script wrote every case's summary into the first output file, because the library remembers the first summary file it opens, so the workflows-only output looked empty.
- What changed: the second version pulls the script out of the real ci.yml and checks each case separately, which also proves it tests the exact code in the PR.

---

## Implementation Notes

**Oct 28, 2026**

Claimed #2244 with a plan (write the reason and changed files to `$GITHUB_STEP_SUMMARY`, try it in a docs-only fork PR) and promised to report back by Tue Nov 3. I asked whether `clang-cross-schedule.yml` belonged in scope.

**Nov 3, 2026**

Missed my own report date. I was working on my Rez contribution and the eld work slipped.

**Nov 5, 2026**

Decided to finish eld first. Wrote the change in ci.yml (one file, +15/-1) and applied it to my fork clone. The patch was applied before I had read it, so I stopped and went through the diff line by line with a scenario (a README typo fix) before committing anything. From then on I asked to see every change before it touched my files.

**Nov 6, 2026**

Had a second agent re-check the change against the whole repo: no code change was needed. Committed with a DCO sign-off, then amended it from my username to my real name. Ran the three fork tests and took signed-in screenshots. Rebased onto upstream main (0d32ea27), which didn't touch ci.yml. Ran the ship-gate until it passed, rewrote the PR description in my own words (on the first pass its voice note made me move the point to the first sentence, and the final pass flagged nothing), and opened #2425 that afternoon.

**Nov 6, 2026 (evening)**

Shankar requested reviews from Steven6798 and quic-shaksuma. Steven asked where to see the summary. I answered with the run link and a screenshot. Steven approved after seeing it in Qualcomm's CI, Shankar approved ("Thanks for your contribution!") and added labels, and Steven merged it at 5:29 PM (merge commit 50d9c1b). Issue #2244 closed. I claimed MetOffice/CSET #926 as a next contribution.

**Nov 6, 2026 (night)**

Caught up on the course tools I'd skipped. I installed the PR monitor with a playbook built from eld's response times, ran it, and decided not to send its merged follow-up. I installed the voice guard and a CLAUDE.local.md for eld, and made the Windows PATH fix permanent. I also installed the scout, ran a scan and graded my picks, then fixed the three rubric and strategy problems that run exposed (see the Unit 5 entry).

**Toolkit**

- Tool: git and the GitHub CLI, run by me in PowerShell, with my AI assistant writing each command and checking the output I pasted back.
- What it produced: the commit, the fork test PRs, the rebase, and the PR itself, all run by me (I don't let the assistant push or post).
- Difficulties: my first commit was signed off as "jl17500" instead of my name, and the patch file wasn't where PowerShell looked for it, because the assistant's file share doesn't save to Downloads by itself.
- What changed: I set `git config user.name "Jing Lam"` and amended the commit before pushing, and the patch was copied into Downloads before `git apply`.

---

## Pull Request

**PR Link:** https://github.com/qualcomm/eld/pull/2425

**PR Description:** Says that when the `changes` job skips the Ubuntu CI build for a docs-only or workflows-only PR, the workflow now writes the reason and the list of changed files to the run summary, and that before, GitHub only showed "skipped". It notes that only ci.yml changes and code PRs are unaffected, links `Fixes #2244`, answers my claim question (`clang-cross-schedule.yml` doesn't run on PRs), lists the three fork runs as testing, and has an AI use section saying what Claude helped with and that I checked it.

**Maintainer Feedback:**

- Nov 6, 2026: Steven6798 asked "Where should we be seeing the summary?" I replied that it shows on the run's Summary page under the job graph, linked the fork run, and attached a screenshot, since Qualcomm's CI was still waiting for workflow approval.
- Nov 6, 2026: Steven6798 replied that he saw it in their CI too ("Looks great!") and approved. Shankar (quic-seaswara) approved with "Thanks for your contribution!", and Steven merged it. Nothing was left to answer, so I didn't post another comment.

**Status:** Merged (Nov 6, 2026, merge commit 50d9c1b)

**Toolkit**

- Tool: the ship-gate (my manifest plus my unit 4 pr-precheck), run by hand before opening the PR.
- What it produced: PASS with all six rubric checks (plan-fidelity, description-accurate, repro-evidence, repo-checks, diff-reviewable, repo-standards) and actionlint ok, after one reject because pr-precheck graded the DCO check "unclear".
- Difficulties: on Windows the gate couldn't find `claude`, my login had expired, and pr-precheck couldn't confirm the DCO sign-off from one-line commits. It also only checks the description I pass it, so every wording change meant another run.
- What changed: I put claude.exe on PATH for the session, logged in again, added the full commit message as DCO evidence in test_evidence.md, and reran the gate after each description edit until it passed on the final text.

---

## Learnings and Reflections

**Technical Skills Gained**

- How GitHub Actions decides to skip a job: one job computes outputs, and another reads them in its `if:`. I also learned that a skipped job can't log anything, and that a job calling another workflow has no steps of its own.
- Writing a run summary from `actions/github-script` with `core.summary`, and using `<code>` instead of markdown backticks inside it.
- Testing CI changes on a fork: turning on Actions, keeping `--repo` on every `gh` command, and why a pull_request run uses the PR's version of the workflow while "Re-run" reuses the old one.
- DCO sign-off, rebasing onto upstream, and `git push --force-with-lease`.

**Challenges Overcome**

- Working out where the message could even live, when the issue's own example put it in a job that can't run (see Understanding the Issue).
- Proving the change instead of describing it: the same README-only PR before and after, plus a script test of the real code.
- Getting the course's ship-gate to run on Windows (see the Pull Request Toolkit block), and answering a reviewer's question within half an hour with a link and a picture rather than an explanation.

**What I'd Do Differently Next Time**

I promised a report by Nov 3 and missed it, because I was juggling Rez at the same time. Next time I'll work one claim at a time, or post a one-line update with a new date on the day I'm going to miss. I'd also set up the Unit 6 voice guard and the Windows PATH fix in the week they were assigned, instead of finding the gaps the day I opened the PR.

---

## Resources Used

- eld's CONTRIBUTING.md (DCO sign-off, opening PRs from a fork): https://github.com/qualcomm/eld/blob/main/CONTRIBUTING.md
- eld's own `delete-old-workflow-runs.yml`, which already writes a run summary with `core.summary`. I matched its style: https://github.com/qualcomm/eld/blob/main/.github/workflows/delete-old-workflow-runs.yml
- actionlint 1.7.12, the version eld's workflow-lint.yml pins: https://github.com/rhysd/actionlint/releases/tag/v1.7.12
- Steven6798's review question on the PR, which showed me that a screenshot answers "where" faster than words: https://github.com/qualcomm/eld/pull/2425#issuecomment-6696526213
- The course's ship-gate INSTALL.md (its notes on `exited 127` and on how manifest rows run in a fresh shell): https://github.com/codepath/ai301-unit7-starter/blob/main/skill/INSTALL.md
