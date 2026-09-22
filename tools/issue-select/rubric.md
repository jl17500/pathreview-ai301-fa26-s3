# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer commit recency | Last 5 default-branch commit dates (repo-facts block in eval mode; repo front page commit history in live mode) | At least one human-authored commit within the last 90 days | required |
| Maintainer response latency | Maintainer first-response sample (repo-facts block in eval mode; recently-updated issues checked for Owner/Member/Collaborator replies in live mode) | At least one Owner/Member/Collaborator reply within 30 days of an issue being opened | preferred |
| Release or push recency | Latest release date and last push to any branch (repo-facts block in eval mode; Releases box / Branches page in live mode) | Latest release or last push to any branch within the last 180 days | required |
| Not archived | Archived flag (repo-facts block in eval mode; archived banner in live mode) | Repo is not marked archived | required |
| Adoption scale | Star count (repo line in eval mode; repo page in live mode) | At least 100 stars | preferred |
| Bounded scope | Issue body and comment thread | Pass when the issue defines one observable deliverable or outcome that a contributor can begin implementing without first choosing missing product behavior. Multiple related files, several examples of the same defect, multiple possible causes, or several suggested implementation approaches still pass when they all serve that one outcome. Fail when it is an umbrella/tracking issue spanning independent work items, an essential behavior/asset/product decision is unspecified or marked TBD, or the thread shows unresolved disagreement about what the result should be | required |
| Not a support question | Issue body | Issue reports a bug or requests a scoped fix or feature, not a how-do-I usage question | required |
| No active claim | Assignees line, linked PRs, and comment thread | No assignee, no open linked PR, and no unanswered claim comment (e.g. "I'll take this") less than 60 days old | required |
| Good-first-issue label | Issue labels | Issue carries a good-first-issue or equivalent label | preferred |
| AI contribution policy | Contribution policy line (repo-facts block in eval mode; CONTRIBUTING.md, AI_POLICY.md, or PR template in live mode) | No outright ban on AI-generated contributions; disclosure, testing, or human-review conditions still pass | required |

## Verdict rule

Accept if every required check grades pass. Any required check graded
fail or unclear rejects the issue. Preferred checks never affect the
verdict; use them only to rank among accepted candidates, with more
preferred passes ranking higher.
