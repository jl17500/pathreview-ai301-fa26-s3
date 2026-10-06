# Unit 3 - Plan and Build

## Posted upstream

**GitHub username**

jl17500

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/18#issuecomment-6018055431

Following my reproduction in https://github.com/codepath/pathreview-ai301-fa26-s3/issues/18#issuecomment-5896030992, I plan to supply the missing file list in GitHubTool.

My run showed file_structure absent and has_tests/has_ci both False. Adding independently retrieved paths to the same input made both flags True. The tool omits that key, while the analyzer already reads it.

I plan to fetch the default branch's recursive Git tree and return its paths as file_structure, using the existing authentication and timeout pattern. Failed or incomplete tree responses will produce a tool failure rather than a successful result with an invented empty list.

The change is limited to agent/tools/github_tool.py and focused tests in tests/unit/test_github_tool.py. I plan to preserve the existing analyzer rules and leave unrelated metadata issues out.

I'll test the real tool-to-analyzer path using mocked GitHub responses, including repositories with and without tests/CI, a non-main branch, authentication, and failed or truncated responses. Then I'll rerun my posted reproduction and expect file_structure to be present and both actual flags to be True.

The extra request adds latency and rate-limit exposure. Large or empty repositories may need follow-up handling; this plan reports unavailable file data as failure rather than assuming tests and CI are absent. I'll report the actual results and any changes to this approach.

## Your branch

**Branch**

fix/18-repo-file-structure

Fork: https://github.com/jl17500/pathreview-fork
Pushed implementation commit: f7ec999

**Evidence**

### Before: posted Unit 2 reproduction

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/18#issuecomment-5896030992

I reproduced the missing-file-list behavior by passing a successful `GitHubTool` result directly to `RepoAnalyzer`. The target repository contains tests and GitHub Actions workflows, but the analyzer reports both `has_tests` and `has_ci` as `False`. Adding the independently retrieved file list to the same input changes both flags to `True`.

### Environment

- Windows, Git Bash (MINGW64), project `.venv` activated.
- Python 3.14.2; Git 2.54.0.windows.1.
- Local fork: https://github.com/jl17500/pathreview-fork
- Local commit: `2f4e82f52efbcfcc57d65b3fa5348672163ca088`.
- Target: https://github.com/codepath/pathreview-ai301-fa26-s3
- Target tree SHA returned during the run: `2f4e82f52efbcfcc57d65b3fa5348672163ca088`.
- Run date: September 29, 2026.
- GitHub requests were unauthenticated. `GitHubTool()` was constructed without a token; the placeholder token in `.env` was not passed to it.

### Setup and reproduction steps

I had completed the repository setup using `docs/SETUP.md` and `make setup`, including installation of the project and development dependencies. For the same source, clone `https://github.com/jl17500/pathreview-fork.git` and check out the local commit listed above. The setup guide has stale repository names; use this fork URL. On my Windows machine, Docker Desktop needed to be started before the containers, and GNU Make needed to be available on PATH. These setup details preceded the reproduction; the check below directly calls the two Python components and does not start the web application.

From the configured checkout, I ran this in Git Bash:

```bash
cd ~/pathreview-fork

if [ -f .venv/Scripts/activate ]; then
  source .venv/Scripts/activate
elif [ -f venv/Scripts/activate ]; then
  source venv/Scripts/activate
fi

python - <<'PY'
import subprocess
import httpx
from agent.tools.github_tool import GitHubTool
from ingestion.parsers.repo_analyzer import RepoAnalyzer

owner = "codepath"
repo = "pathreview-ai301-fa26-s3"
api = f"https://api.github.com/repos/{owner}/{repo}"

print("Local commit:", subprocess.check_output(
    ["git", "rev-parse", "HEAD"], text=True
).strip())
print("Target:", f"https://github.com/{owner}/{repo}")
print("Authentication: unauthenticated")

with httpx.Client(timeout=30) as client:
    response = client.get(api)
    response.raise_for_status()
    branch = response.json()["default_branch"]

    response = client.get(f"{api}/git/trees/{branch}",
                          params={"recursive": "1"})
    response.raise_for_status()
    tree = response.json()
    if tree.get("truncated"):
        raise RuntimeError("File tree truncated; cannot use as complete evidence.")
    paths = [item["path"] for item in tree["tree"]]

tests = [p for p in paths if p.startswith(("tests/", "test/"))]
workflows = [p for p in paths if p.startswith(".github/workflows/")]

print("Target tree SHA:", tree["sha"])
print("Test path examples:", tests[:5])
print("Workflow paths:", workflows)

if not tests or not workflows:
    raise RuntimeError("Target needs both tests and workflows for this check.")

result = GitHubTool().execute({
    "github_username": owner,
    "repo_name": repo,
})
print("Tool success:", result.success)
if not result.success:
    raise RuntimeError(f"GitHubTool failed: {result.error}")

print("Returned keys:", sorted(result.data))
print("file_structure present:", "file_structure" in result.data)

analyzer = RepoAnalyzer()
actual = analyzer.parse(result.data)
print("ACTUAL has_tests:", actual.metadata["has_tests"])
print("ACTUAL has_ci:", actual.metadata["has_ci"])

# Diagnostic control: add the independently retrieved file list.
control = analyzer.parse({**result.data, "file_structure": paths})
print("CONTROL with file list, has_tests:", control.metadata["has_tests"])
print("CONTROL with file list, has_ci:", control.metadata["has_ci"])
PY
```

The script checks the live default branch, so a later run may return a different target SHA. The SHA above records the tree used in my run. API errors and truncated trees stop the script rather than being counted as reproduction evidence.

### Expected behavior

For this repository, which has a `tests/` directory and `.github/workflows/ci.yml` and `.github/workflows/eval.yml`, the tool-to-analyzer input should provide enough file information for the analyzer to report `has_tests=True` and `has_ci=True`.

### Observed output

```text
Local commit: 2f4e82f52efbcfcc57d65b3fa5348672163ca088
Target: https://github.com/codepath/pathreview-ai301-fa26-s3
Authentication: unauthenticated
Target tree SHA: 2f4e82f52efbcfcc57d65b3fa5348672163ca088
Test path examples: ['tests/__init__.py', 'tests/benchmarks', 'tests/benchmarks/__init__.py', 'tests/conftest.py', 'tests/fixtures']
Workflow paths: ['.github/workflows/ci.yml', '.github/workflows/eval.yml']
2026-09-29 14:00:47 [info     ] github_repo_fetched            language=Python repo=pathreview-ai301-fa26-s3 stars=6 username=codepath
Tool success: True
Returned keys: ['description', 'fork_count', 'has_readme', 'homepage', 'last_commit_date', 'name', 'open_issues_count', 'primary_language', 'star_count', 'topics']
file_structure present: False
ACTUAL has_tests: False
ACTUAL has_ci: False
CONTROL with file list, has_tests: True
CONTROL with file list, has_ci: True
```

### Outcome and scope

The tool succeeded, but its returned dictionary did not contain `file_structure`. Both detection flags were `False` despite the independently verified test and workflow paths. For the diagnostic control, I added only `file_structure` to a copy of the returned dictionary; both flags became `True`.

This supports the reported missing-file-list problem at the direct tool-to-parser boundary for this repository and commit. I did not run the full application workflow or test every repository. The control changed the input in memory; I did not apply a source-code fix.

### After: saved live reproduction log

This run tested base 2f4e82f52efbcfcc57d65b3fa5348672163ca088 plus the working-tree fix, before it was committed. The implementation was subsequently committed as f7ec999. Do not interpret the base HEAD printed by the script as containing the fix.

```text
$ source .venv/Scripts/activate && python unit2-repro.py   (script extracted verbatim from unit2-repro-draft.md, run from repo root)
Local commit: 2f4e82f52efbcfcc57d65b3fa5348672163ca088
Target: https://github.com/codepath/pathreview-ai301-fa26-s3
Authentication: unauthenticated
Target tree SHA: 2f4e82f52efbcfcc57d65b3fa5348672163ca088
Test path examples: ['tests/__init__.py', 'tests/benchmarks', 'tests/benchmarks/__init__.py', 'tests/conftest.py', 'tests/fixtures']
Workflow paths: ['.github/workflows/ci.yml', '.github/workflows/eval.yml']
2026-10-06 10:19:21 [info     ] github_repo_fetched            language=Python repo=pathreview-ai301-fa26-s3 stars=6 username=codepath
Tool success: True
Returned keys: ['description', 'file_structure', 'fork_count', 'has_readme', 'homepage', 'last_commit_date', 'name', 'open_issues_count', 'primary_language', 'star_count', 'topics']
file_structure present: True
ACTUAL has_tests: True
ACTUAL has_ci: True
CONTROL with file list, has_tests: True
CONTROL with file list, has_ci: True
EXIT CODE: 0

```

### Other validation

The latest focused run passed 23 tests. Focused Ruff and the mypy commit hook passed. The unit suite reported 398 passed and 53 xfailed. make check failed at full-project mypy due to NumPy stub syntax versus the Python 3.11 target; the identical failure occurred on pristine HEAD. Full-project typechecking remains unverified. The detailed record is in plan.md.

## Eval iterations

**Run history**

I used the assignment's fallback because our group activity did not produce a completed rubric and procedure.

1. Calibration smoke check: calib-01 accepted, matching gold. This was not scored.
2. First and only full evaluation: 19/20. Every category had at least one match. No rubric revisions or further eval runs followed.

Exact agreement line from the submitted run:

```text
agreement: 19/20 scored items  (bar: 18/20: PASS)
```

**Package analysis**

For pkg-14, my skill returned reject while the gold label was accept. The failed checks were diagnosis-grounded and uncertainty-honest.

The plan states that "the reattach path wires the client's stdin to the session before the query responses have been consumed". My grader treated that mechanism as unconfirmed: the reproduced observations establish a reattach-only leak, a regression window, and a cache effect, but do not themselves establish the input-wiring mechanism. The plan describes debug tracing, but the grader did not treat that description as sufficient evidence for the asserted cause.

The grader also flagged the comment's claim "0.44.2 on leaking": the recorded reproduction used 0.44.3, while 0.44.2 came from the original issue. The gold label accepts the package despite those concerns. I interpret this disagreement as my checks requiring a clearer distinction between observed behavior and inferred explanation.

**Check rationale**

Exact diagnosis-grounded row from the submitted rubric:

```text
| diagnosis-grounded | The candidate plan's stated cause compared with the issue and repro-evidence block; see Diagnosis and grounding | Pass if the diagnosis explains the reproduced behavior without contradicting the evidence. An unconfirmed cause must be presented as a hypothesis with a concrete way to check it. Fail if the plan ignores the repro, contradicts it, or treats an unsupported guess as established. | required |
```

I used this check to make a plan explain the reproduced behavior while keeping an unconfirmed cause testable. I rejected accepting a plausible explanation solely because it sounds convincing. The hypothesis-and-verification allowance lets a contributor proceed without pretending that the full mechanism has already been proved.

**Trade-offs**

This strictness can reject a useful plan whose cause is plausible and whose next step would verify it. pkg-14 is the concrete example: the gold label accepts it, but my skill rejects its certainty about the mechanism and tested versions. I kept the rubric unchanged after the 19/20 run, accepting this false rejection rather than loosening the evidence standard just to match one label.

Live grading also exposed a limitation: the first draft's test location differed from the repository convention, but the grader flagged it without rejecting. I corrected the plan to use tests/unit/test_github_tool.py and the unit marker, then rechecked the plan. I did not change the grading skill after its saved eval.