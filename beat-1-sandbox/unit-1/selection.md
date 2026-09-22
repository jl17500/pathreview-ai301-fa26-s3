# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/18

**Verdict output**

Claude graded three live candidates using my installed `issue-select` skill. Issue #18 ranked first among the accepted issues. The fenced JSON below was reconstructed from Claude's grading output after the terminal copy corrupted its formatting.

Accepted, in fit order:

1. #18 — Repo analyzer never receives a file list — every required check passes. Best fit: a bounded Python bug with the root cause already located, a clear expected behavior (`has_tests` / `has_ci` should stop always returning `False`), and an obvious way to test it.

2. #12 — Snapshot tests for prompt templates — every required check passes, but it is more of a test-design task than debugging an existing bug.

Rejected:

#25 — Safety monitoring, multi-turn filter bypass — rejected by Bounded scope because the expected bypass sequences and cross-request correlation behavior were not defined.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/18",
    "checks": [
      {
        "name": "Maintainer commit recency",
        "grade": "pass",
        "evidence": "Last main commit 2026-09-16 by Andrew Burke (Aburke225), human-authored, 6 days before today"
      },
      {
        "name": "Maintainer response latency",
        "grade": "pass",
        "evidence": "Aburke225 (COLLABORATOR) replied 2026-09-16 on issue #52 opened 2026-09-10 — 6 days"
      },
      {
        "name": "Release or push recency",
        "grade": "pass",
        "evidence": "No releases published, but last push to main was 2026-09-16, within 180 days"
      },
      {
        "name": "Not archived",
        "grade": "pass",
        "evidence": "Repo API reports archived: false; no archived banner"
      },
      {
        "name": "Adoption scale",
        "grade": "fail",
        "evidence": "stargazers_count = 3, below the 100-star threshold"
      },
      {
        "name": "Bounded scope",
        "grade": "pass",
        "evidence": "One outcome — supply a file list under file_structure so has_tests/has_ci work; github_tool.py and repo_analyzer.py both serve that outcome"
      },
      {
        "name": "Not a support question",
        "grade": "pass",
        "evidence": "The issue reports a concrete defect: no code sets file_structure, so has_tests and has_ci are always False"
      },
      {
        "name": "No active claim",
        "grade": "pass",
        "evidence": "No assignees, no comments, and no linked pull requests"
      },
      {
        "name": "Good-first-issue label",
        "grade": "pass",
        "evidence": "Labels include good first issue"
      },
      {
        "name": "AI contribution policy",
        "grade": "pass",
        "evidence": "CONTRIBUTING.md and the pull request template contain no AI restriction"
      }
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/12",
    "checks": [
      {
        "name": "Maintainer commit recency",
        "grade": "pass",
        "evidence": "Last main commit 2026-09-16 by Andrew Burke (Aburke225), human-authored, 6 days before today"
      },
      {
        "name": "Maintainer response latency",
        "grade": "pass",
        "evidence": "Maintainer response sample included a collaborator reply within 6 days"
      },
      {
        "name": "Release or push recency",
        "grade": "pass",
        "evidence": "Last push was 2026-09-16, within 180 days"
      },
      {
        "name": "Not archived",
        "grade": "pass",
        "evidence": "Repository is not archived"
      },
      {
        "name": "Adoption scale",
        "grade": "fail",
        "evidence": "Repository has 3 stars, below the preferred 100-star threshold"
      },
      {
        "name": "Bounded scope",
        "grade": "pass",
        "evidence": "One test-focused deliverable and the template versioning mechanism already exists"
      },
      {
        "name": "Not a support question",
        "grade": "pass",
        "evidence": "Scoped enhancement requesting snapshot tests"
      },
      {
        "name": "No active claim",
        "grade": "pass",
        "evidence": "No assignees, comments, or linked pull requests"
      },
      {
        "name": "Good-first-issue label",
        "grade": "pass",
        "evidence": "Labels include good first issue"
      },
      {
        "name": "AI contribution policy",
        "grade": "pass",
        "evidence": "No AI contribution ban was found"
      }
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/25",
    "checks": [
      {
        "name": "Maintainer commit recency",
        "grade": "pass",
        "evidence": "Recent human maintainer commit"
      },
      {
        "name": "Maintainer response latency",
        "grade": "pass",
        "evidence": "Maintainer response sample met the preferred threshold"
      },
      {
        "name": "Release or push recency",
        "grade": "pass",
        "evidence": "Recent repository push"
      },
      {
        "name": "Not archived",
        "grade": "pass",
        "evidence": "Repository is not archived"
      },
      {
        "name": "Adoption scale",
        "grade": "fail",
        "evidence": "Repository has 3 stars"
      },
      {
        "name": "Bounded scope",
        "grade": "fail",
        "evidence": "Known bypass sequences are not defined and the monitoring code has no specified session/user correlation mechanism, leaving essential behavior undecided"
      },
      {
        "name": "Not a support question",
        "grade": "pass",
        "evidence": "The issue describes a concrete monitoring defect"
      },
      {
        "name": "No active claim",
        "grade": "pass",
        "evidence": "No assignees, comments, or linked pull requests"
      },
      {
        "name": "Good-first-issue label",
        "grade": "fail",
        "evidence": "No good-first-issue label"
      },
      {
        "name": "AI contribution policy",
        "grade": "pass",
        "evidence": "No AI contribution ban was found"
      }
    ],
    "verdict": "reject"
  }
]
```

---

## Eval iterations

**Run history**

My scored eval runs, in order:

* Initial 3-item smoke run: 2/3
* `issue-01` diagnostic: 0/1
* `issue-01, issue-14` diagnostic: 1/2
* `issue-01` after scope revision: 1/1
* `issue-05, issue-10` scope canaries: 2/2
* Six-category diagnostic batch: 5/6
* Scope canaries `issue-01, issue-05, issue-10, issue-20`: 4/4
* Full run: 16/20
* Revised scope canaries: 6/6
* Full run: 18/20 — PASS
* Final saved full run: 19/20 — PASS

**Issue analysis**

I focused on `issue-20`. My rubric originally returned **accept**, while the gold label was **reject**. The issue looked small because it asked for one toolbar feature, but the logo asset itself was still marked TBD. That meant a contributor could not implement the requested behavior without first deciding an essential product detail. I revised the Bounded scope check so an issue fails when an essential behavior, asset, or product decision is still unspecified. After that change, `issue-20` correctly rejected while accepted issues such as `issue-01`, `issue-04`, and `issue-19` still passed the scope check.

**Check rationale**

Current rubric wording:

> "Pass when the issue defines one observable deliverable or outcome that a contributor can begin implementing without first choosing missing product behavior. Multiple related files, several examples of the same defect, multiple possible causes, or several suggested implementation approaches still pass when they all serve that one outcome. Fail when it is an umbrella/tracking issue spanning independent work items, an essential behavior/asset/product decision is unspecified or marked TBD, or the thread shows unresolved disagreement about what the result should be."

I wrote it this way because my earlier version treated multiple files or multiple possible causes as signs that an issue was too broad. That incorrectly rejected issues that still had one clear outcome. The revised check focuses on whether the contributor knows what result they are trying to produce, not how many files or possible causes are involved.

**Trade-offs**

This check favors issues with a clearly specified outcome, so it can reject an issue that an experienced contributor might still be comfortable exploring. I accepted that trade-off because this rubric is specifically for a first contribution. I tested the change against `issue-01`, `issue-04`, `issue-05`, `issue-10`, `issue-19`, and `issue-20`; the three accepted issues stayed accepted and the three scope rejects stayed rejected.

---

## Selection rationale

**Selection rationale**

1. I chose issue #18 because it fits the kind of work I want to do. It is a Python bug with a specific failure, a small set of relevant files, and a clear way to test whether the fix works. The estimated 2–4 hour scope also feels realistic for my first contribution.

2. The verdict correctly identified that the repository is active, the issue is unclaimed, the bug is bounded, and it has a good-first-issue label. The rubric could not fully capture that I personally prefer debugging an existing behavior over designing new tests from scratch, which is why I preferred #18 over the also-accepted #12.

3. I expect claiming it to be straightforward because there are no assignees, comments, or linked pull requests. The main challenge will probably be understanding how `GitHubTool` passes repository metadata into `repo_analyzer.py`, not competing with another contributor.
