# Procedure: how this skill grades a plan package

## Read order

1. Identify live or eval mode from the request. In live mode, read scope.md first and confirm the issue is in scope. Stop if the repository is outside scope or the scope is unfilled. In eval mode, ignore scope.md and use only the supplied bundle.
2. Read rubric.md and references/evidence-guide.md. In live mode, also read voice-guide.md.
3. Read the issue, repository facts or applicable policies, and thread highlights. Record the reported trigger, expected behavior, and relevant maintainer directions.
4. Read the reproduction evidence before the candidate plan. Record what was run, what was observed, and what the evidence does and does not establish.
5. Read the complete candidate plan and comment, including any deviations. Record the proposed cause, change, boundaries, implementation steps, tests, and uncertainties. Do not grade until the whole package has been read.

## Evidence gathering

1. For diagnosis-grounded and cause-addressed, pair the plan's cause and proposed change with specific observations from the repro. Record contradictions and distinguish observations from hypotheses.
2. For scope-bounded, collect the included work, excluded work, and affected files or areas. Identify whether each proposed edit serves the reported issue.
3. For steps-executable, collect the starting location, intended behavioral change, and any prerequisite investigation or implementation order.
4. For tests-decisive, pair each relevant repro trigger with the proposed test and its expected observable result. Note affected neighboring behavior and whether the proposed checks cover it.
5. For uncertainty-honest, compare claims with available evidence. Record material assumptions, how they will be checked, and explanations for any known deviations.
6. For thread-and-conventions, compare the comment with the plan, maintainer directions, and policies that apply to comments or the proposed contribution. Distinguish an explicit restriction from a preference and distinguish comment disclosure rules from PR-only rules.
7. In eval mode, quote only bundle evidence and do not fetch outside information. In live mode, use the issue thread, the student's posted repro, and applicable repository documents. Grade the submitted drafts as written; do not fill their gaps with unrelated local files. For a house issue, use the house repro evidence quoted in the drafts.
8. For each check, retain a short deciding quote or specific fact. If evidence is missing, record exactly what is absent instead of inventing it.

## Check execution

1. Execute every check in rubric order using the gathered evidence.
2. Assign pass when the pass condition is supported. Assign fail when evidence contradicts it or a required element is demonstrably absent. Assign unclear when the necessary evidence is genuinely unavailable or ambiguous.
3. Before assigning unclear, revisit the relevant evidence location once. In live mode, report access failures rather than interpreting an inaccessible policy as no policy. Do not browse in eval mode.
4. Judge the plan's meaning and behavior, not its length, headings, or number of files. A concise plan or a manual test can pass.
5. Do not require the build to have happened before grading a plan. Evaluate proposed tests as proposals; reject unsupported claims that they already passed.
6. Reuse gathered evidence when sufficient. Re-read only the portions needed to resolve a concrete ambiguity.
7. In live mode, separately report any voice-guide violation and quote the rule. It changes the verdict only if a rubric check also fails.
8. Report a procedure gap if an essential grading step is unspecified. Do not silently invent a new rubric requirement.

## Verdict assembly

1. List every check with its pass, fail, or unclear grade and a short deciding evidence quote or fact.
2. Apply the rubric's verdict rule exactly: all required checks pass means accept; any required fail or unclear means reject.
3. Briefly identify the checks responsible for a rejection. Do not replace the defined rule with an overall impression.
4. End with the fenced JSON object required by SKILL.md: item, checks containing name/grade/evidence, and verdict. Use the package ID in eval mode or the issue URL in live mode. Write nothing after that block.