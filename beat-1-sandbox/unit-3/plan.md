# Plan for issue #18: supply repository file paths

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/18
Author: jl17500
Starting commit: 2f4e82f52efbcfcc57d65b3fa5348672163ca088
Planned branch: fix/18-repo-file-structure

## Diagnosis and evidence

My reproduction:
https://github.com/codepath/pathreview-ai301-fa26-s3/issues/18#issuecomment-5896030992

On Windows with Python 3.14.2, I called GitHubTool without authentication against codepath/pathreview-ai301-fa26-s3 and passed its output directly to RepoAnalyzer.

An independent GitHub tree request found tests/__init__.py, tests/conftest.py, .github/workflows/ci.yml, and .github/workflows/eval.yml.

My observed output was:

    Tool success: True
    file_structure present: False
    ACTUAL has_tests: False
    ACTUAL has_ci: False
    CONTROL with file list, has_tests: True
    CONTROL with file list, has_ci: True

The control added the independently retrieved paths to a copy of the same metadata dictionary.

GitHubTool._fetch_repo_metadata does not set file_structure. RepoAnalyzer._detect_tests and _detect_ci read that key and default to an empty string. IngestionPipeline.ingest_repo_metadata passes its metadata dictionary directly to RepoAnalyzer.

This supports a missing input at the direct tool-to-analyzer boundary. I have not tested the full application workflow.

## Scope

In scope:
- Retrieve the default branch's repository paths during GitHub metadata collection.
- Return those paths under file_structure.
- Preserve existing metadata fields and analyzer detection rules.
- Add focused tests for detection and retrieval failures.

Out of scope:
- Changing filename detection heuristics.
- Fixing other metadata naming differences or README handling.
- Changing orchestration, databases, embeddings, or the frontend.
- Adding caching, retries, or a general repository crawler.
- Recovering a tree response that GitHub marks truncated.

## Files

Implementation:
- agent/tools/github_tool.py

New tests:
- tests/unit/test_github_tool.py

Read for compatibility:
- ingestion/parsers/repo_analyzer.py
- ingestion/pipeline.py
- tests/conftest.py
- pyproject.toml

No analyzer or pipeline edit is currently planned. If one becomes necessary, record the reason under Deviations and recheck the plan.

## Approach

1. Inspect existing test fixtures and HTTP mocking conventions.
2. Add a regression test that mocks GitHub HTTP responses, calls the real GitHubTool.execute, and passes its result to the real RepoAnalyzer.parse. Use paths containing tests and a GitHub Actions workflow. Run it before the implementation and save its actual failure.
3. Add a small file-list retrieval helper in GitHubTool. Use default_branch from the existing repository metadata request rather than assuming main.
4. Request that branch's recursive Git tree with the existing optional token and a finite timeout. Encode the branch ref safely in the URL.
5. Extract repository-relative paths and include the list as file_structure in the returned metadata.
6. Treat HTTP errors, timeouts, missing usable branch data, malformed tree responses, and truncated trees as retrieval failures through the existing execute error handling. Do not report success with an invented empty list. A valid complete empty tree may return an empty list.
7. Run focused tests and lint, review every diff, and rerun my posted Unit 2 reproduction.

## Test plan

### Automated regression

Run from the fork root in PowerShell:

    .\.venv\Scripts\python.exe -m pytest tests/unit/test_github_tool.py -q

Before the fix, the positive regression should fail because file_structure is absent.

After the fix, verify:
- Paths containing tests/test_example.py and .github/workflows/ci.yml appear in file_structure, and the actual tool result produces has_tests=True and has_ci=True through RepoAnalyzer.
- A complete tree containing only README.md and src/app.py produces False for both flags.
- A valid empty tree produces an empty list and False flags.
- A non-main default branch, including a slash in its name, is handled correctly.
- Authentication is passed to the tree request when a token is supplied, and Authorization is omitted when no token is supplied.
- HTTP errors, timeouts, malformed responses, and truncated trees return an unsuccessful ToolResult.
- Existing metadata values remain unchanged apart from the added key.

Mock the HTTP boundary. Do not mock the file-list helper or analyzer detection methods in the main regression.

### Real reproduction

Rerun the exact script in my posted Unit 2 reproduction with the project environment. It independently checks the target repository's paths before calling the real tool and analyzer.

Expected output after the fix:

    Tool success: True
    file_structure present: True
    ACTUAL has_tests: True
    ACTUAL has_ci: True
    CONTROL with file list, has_tests: True
    CONTROL with file list, has_ci: True

Save the actual commands and output, the tested commit, and the observed target tree SHA. Use my posted Week 2 output as the original before evidence. A network or rate-limit error means the live check is blocked, not passed.

### Focused lint

Run:

    .\.venv\Scripts\python.exe -m ruff check agent/tools/github_tool.py tests/unit/test_github_tool.py

## Risks and unknowns

- The extra request adds latency and uses API quota. Reuse the existing authentication approach and a finite timeout.
- Large repositories may return truncated trees. Reject incomplete data rather than claiming tests or CI are absent.
- Empty repositories or unavailable branch refs may make the request fail. Report failure rather than interpreting an arbitrary 404 as an empty repository.
- Metadata collection can now fail even when the first request succeeds. This is an intentional trade-off to avoid detection based on missing file data.
- Existing filename heuristics may still misclassify unusual layouts. Changing them is outside this issue.
- Inspect test fixtures for environment requirements before adding tests and report any actual blocker.
- The live branch can change between requests. Record its observed SHA and use deterministic mocked responses for regression tests.
- Implementation and after-fix tests are complete; see Deviations for the results.

## Repository test conventions

Place the new tests in tests/unit/test_github_tool.py and mark the module with pytestmark = pytest.mark.unit so the repository's unit-test command selects them.

After the focused regression and lint checks, run from the fork root:

    make test-unit
    make check

Save the actual results. If either command fails, investigate whether the failure comes from this change, an existing issue, or the environment. Do not claim a blocked or failed check passed.

## Deviations

Implementation followed the revised, posted plan. Only agent/tools/github_tool.py and tests/unit/test_github_tool.py changed. No analyzer, pipeline, or configuration edit was needed.

Test adjustment after the first commit attempt: the mypy pre-commit hook rejected two lines in tests/unit/test_github_tool.py. In the regression and the two detection tests, the real tool output is now JSON-serialized with json.dumps before it is passed to the real RepoAnalyzer.parse, matching parse's declared input type (str | bytes). The tool's data and the parser are still the real ones. The REPO_JSON and TREE_JSON fixtures also gained explicit dict[str, Any] annotations. No type-ignore comments, casts, or disabled checks were used. Production code, behavior, and scope are unchanged.

Observation, not a deviation: an HTTP 404 on the tree request goes through the existing execute handler and is reported as "Repository not found", which is that handler's wording for any 404. It is still an unsuccessful ToolResult and is not treated as an empty repository.

## Verification results

Tested state: base commit 2f4e82f52efbcfcc57d65b3fa5348672163ca088 plus the uncommitted implementation and test changes in the working tree. Nothing is committed. The live reproduction script prints "Local commit: 2f4e82f..." because it reports the base HEAD.

Regression before the fix: the test failed on `assert "file_structure" in result.data` with the tool result otherwise successful. That failure was shown during the build; it was not saved to a log file.

After the fix:
- Focused pytest, `.\.venv\Scripts\python.exe -m pytest tests/unit/test_github_tool.py -q`: 23 passed in 0.90s.
- Focused lint, `ruff check agent/tools/github_tool.py tests/unit/test_github_tool.py`: All checks passed.
- `make test-unit`: exit 0. 398 passed, 53 xfailed, 1 warning in 22.07s.
- `make check`: exit 2. It did not pass.
  - `ruff check .` passed, and `black .` left 111 files unchanged.
  - `mypy api/ core/ ingestion/ rag/ agent/ safety/` stopped with `numpy\__init__.pyi:737: error: Type statement is only supported in Python 3.12 and greater [syntax]` and `Found 1 error in 1 file (errors prevented further checking)`.
  - Cause: the environment. pyproject.toml sets mypy `python_version = "3.11"`, while the venv has Python 3.14.2 and numpy 2.5.3, whose stubs use 3.12 syntax.
  - The same error and exit 2 occurred on a pristine export of HEAD without this change.
  - Mypy aborted before checking repository code, so full-project typechecking remains unverified. `mypy agent/tools/github_tool.py` alone reported "Success: no issues found in 1 source file". That is supplementary and does not replace `make typecheck`.
- Live reproduction (unauthenticated, exact script from unit2-repro-draft.md): exit 0. Observed target tree SHA 2f4e82f52efbcfcc57d65b3fa5348672163ca088.

      Tool success: True
      file_structure present: True
      ACTUAL has_tests: True
      ACTUAL has_ci: True
      CONTROL with file list, has_tests: True
      CONTROL with file list, has_ci: True

  The posted Week 2 output (file_structure present: False, ACTUAL has_tests False, ACTUAL has_ci False) is the before evidence.

Logs are saved outside the repository in ~/unit3-evidence/. They predate the test adjustment above and were not regenerated.

After the test adjustment (rerun on the working tree, output seen but not saved to a log file):
- Focused pytest: 23 passed in 0.91s.
- Focused lint: All checks passed.
- `pre-commit run mypy --files agent/tools/github_tool.py tests/unit/test_github_tool.py`: Passed, exit 0.
- The mypy hook pass does not replace the full-project result. `make check` still exited 2 at mypy on the numpy stub error described above, and full-project typechecking remains unverified. `make test-unit` (398 passed, 53 xfailed) was run before the test adjustment and has not been rerun since.