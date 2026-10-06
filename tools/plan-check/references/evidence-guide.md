# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**Where it lives:** In eval mode, compare the candidate plan's cause and proposed change with the Issue and Repro evidence sections. In live mode, compare plan.md and comment.md with the issue and the student's posted reproduction, or the house repro evidence quoted in the drafts.

**What good looks like:** The cause explains the observed failure, and the change addresses that cause. A plausible hypothesis is acceptable when labeled and paired with a concrete verification step; hiding the symptom or contradicting the repro is not.

## Scope

**Where it lives:** In eval mode, read the candidate plan's change, in-scope and out-of-scope statements, and named files or areas. In live mode, find the same information in plan.md.

**What good looks like:** The change has a clear boundary and each supporting edit serves the issue. Several necessary files can still form one bounded change; an unrelated rewrite does not.

## Executability

**Where it lives:** In eval mode, read the candidate plan's approach, named components, and order of work. In live mode, read these in plan.md, checking any referenced repository documents where needed.

**What good looks like:** A stranger can locate the starting point and understand the intended behavioral change. Investigation is actionable when it names what to inspect and how the finding determines the next step.

## Test plan

**Where it lives:** In eval mode, compare the candidate test plan with the repro-evidence block's inputs, actions, and output. In live mode, compare plan.md's tests with the student's posted reproduction or quoted house repro.

**What good looks like:** The test reaches the affected code using the reproduced trigger and specifies what must change after the fix. Relevant regression or failure checks cover behavior put at risk by the approach. A precise manual check can be sufficient.

## Honesty

**Where it lives:** In eval mode, compare the plan and comment's claims, assumptions, risks, and any deviation notes with the issue and repro evidence. In live mode, use the same parts of the drafts and their cited evidence, including the Deviations section for a revised plan.

**What good looks like:** Observations, hypotheses, and future actions are distinguishable. Material unknowns have a way to be resolved, and known deviations say what changed and why. A pre-build plan need not claim completed tests or invent deviations.

## Comms

**Where it lives:** In eval mode, compare the Candidate plan comment with Thread highlights, Repo facts, and the candidate plan. In live mode, compare comment.md with the issue thread, plan.md, and applicable CONTRIBUTING, template, or AI-use policies.

**What good looks like:** The comment explains this plan accurately and responds to relevant maintainer directions, including limits on outside PRs or review availability. It follows policies within their stated scope, including disclosure when required for comments. A classmate's claim does not block a separate course contribution.