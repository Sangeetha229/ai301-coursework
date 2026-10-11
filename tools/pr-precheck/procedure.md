# Procedure: how this tool grades a PR package

## Read order

1. Read `plan.md` first. Record the diagnosis, intended behavior, files to change, files excluded from changes, approach, test plan, risks, and stated deviations. Use the accepted plan as the baseline for evaluating the actual PR.

2. Read the repository's contribution guide, PR template, and applicable issue requirements. Record required tests, documentation or changelog updates, issue references, checklists, and other submission rules.

3. Read the PR description or submission record. Record what the author claims changed, which files were changed, what behavior was fixed, and which tests or checks passed. Treat these statements as claims that must be verified.

4. Read `test_evidence.md`. Record each command, expected result, observed output, and assessment. Identify tests that were run, tests that failed, checks that were skipped, and claims without supporting output.

5. Inspect the actual diff and changed-file summary. Record every changed file and compare the changes with the plan's scope, exclusions, and intended behavior.

6. Compare the PR description with both the diff and test evidence. Identify claims that are unsupported, inaccurate, or inconsistent with the actual changes or observed results.

7. Review any additional evidence referenced by the package. Before grading, summarize the planned scope, actual changes, verification evidence, description accuracy, and applicable repository requirements.

Read the plan before the diff because the plan defines the expected scope. Read the test plan before evaluating test evidence because it identifies what must be verified. Compare the PR description with the diff and test evidence so the grading reflects what was actually changed and verified, not just what the author claims.

## Evidence gathering

### 1. Silent drift

1. Extract the planned files, intended behavior, explicit exclusions, and approach from `plan.md`.
2. Run or inspect the available changed-file summary and actual diff. Record every changed file and the purpose of its changes.
3. Compare the actual diff against the planned scope and exclusions. Look for unapproved behavior changes, unrelated features, configuration changes, and refactoring.
4. Compare the PR description and deviation statement with the actual diff. Record any mismatch between the claimed and implemented changes.
5. Mark `pass` when the changes follow the accepted plan and the description accurately represents the diff. Mark `fail` when evidence demonstrates unapproved changes or materially inaccurate claims. Mark `unclear` when the necessary evidence is unavailable.

### 2. Not tested

1. Extract the required test commands, expected results, regression checks, and control cases from the plan's test plan.
2. Match each planned check to the corresponding command and observed output in `test_evidence.md`.
3. Distinguish actual command output from the author's assessment or a claim that a check passed.
4. Compare the recorded results with the planned behavior and applicable repository testing requirements. Record missing, failed, skipped, or insufficiently supported checks.
5. Do not require unit tests or integration tests automatically for every documentation-only change. Evaluate verification against the actual change, the plan, and repository requirements.
6. Mark `pass` when required verification is adequately supported by evidence. Mark `fail` when evidence demonstrates a required check failed or was skipped contrary to requirements. Mark `unclear` when the evidence is insufficient to determine whether a required condition was met.

### 3. Unreviewable

1. Inspect the complete diff and all changed files, not only the files mentioned in the PR description.
2. Look for unrelated edits, unnecessary refactoring, debugging statements, commented-out alternatives, unused helpers, abandoned code, duplicated content, and changes that obscure the intended fix.
3. Compare extra changes with the plan to determine whether they make the implementation unnecessarily difficult to understand or review.
4. Record the specific file, diff hunk, or code detail that supports the assessment. Distinguish a material reviewability problem from a suggestion for optional cleanup.
5. Mark `pass` when the diff is focused, understandable, and reviewable. Mark `fail` when evidence demonstrates material reviewability problems. Mark `unclear` when the relevant diff is unavailable or incomplete.

### 4. Standards wall

1. Identify applicable requirements in the contribution guide, PR template, issue instructions, and other repository documentation.
2. Record which requirements apply to the specific change. Do not invent requirements or apply unrelated repository rules.
3. Compare each applicable requirement with the actual diff, test evidence, PR description, and supplied submission artifacts.
4. Verify required documentation, changelog entries, tests, issue references, checklists, formatting, and policy disclosures where applicable.
5. Inspect the relevant files and evidence before concluding that a required item is missing. If the package does not establish whether a requirement was satisfied, record the uncertainty.
6. Mark `pass` when applicable repository requirements are satisfied. Mark `fail` when evidence demonstrates a requirement was violated. Mark `unclear` when evidence is insufficient to establish compliance.

## Check execution

1. Grade the checks in this fixed order:

   1. Silent drift
   2. Not tested
   3. Unreviewable
   4. Standards wall

2. Use the evidence gathered during the read order. Reopen a file or diff hunk only when a claim needs verification or the available evidence is insufficient.

3. Apply the corresponding pass condition in `rubric.md` to each check. Assign exactly one grade: `pass`, `fail`, or `unclear`.

4. Write one concise, evidence-based explanation for each grade. Identify the relevant file, command output, diff change, plan requirement, or repository rule.

5. When evidence is absent, distinguish between a demonstrated violation and an unresolved condition:

   * Use `pass` only when evidence supports the pass condition.
   * Use `fail` when evidence demonstrates a violation.
   * Use `unclear` when evidence is insufficient to decide.
   * Never treat missing evidence as a pass or invent test results, repository rules, or implementation details.

6. Grade each check independently. A passing check does not compensate for a failing or unclear check.

7. Use the same four check names and grading rules for every PR package. Do not introduce additional checks or change the names defined in `rubric.md`.

8. Follow the output structure specified in `SKILL.md` and `CONTRACT.md`. Do not add fields or change the required grade and verdict values.

## Verdict assembly

1. Apply the verdict rule in `rubric.md` after grading all four checks:

   * **Accept:** all four required checks are `pass`.
   * **Reject:** any required check is `fail` or `unclear`.

2. Do not average grades or allow passing checks to override a failing or unclear check.

3. If all checks pass, return `accept` with an evidence statement for each check.

4. If any check fails or is unclear, return `reject`. Select the first non-passing check in rubric order as the deciding check.

5. In the deciding check's evidence statement, quote or identify the specific fact that determined the rejection. Include the relevant file, diff detail, command result, plan requirement, or repository rule when available.

6. If multiple checks fail or are unclear, retain the evidence for every check in the output, but use the first non-passing check in rubric order to explain the deciding reason.

7. Before returning the result, confirm that all four checks appear exactly once, every grade is valid, the verdict follows the rubric, and every evidence statement is supported by the package. Do not present unrun tests or unsupported claims as facts.