# Rubric: is this pull request ready to submit?

## Checks


| Check          | Evidence                                                                                                                                              | Pass condition                                                                                                                                                                                                                                   | Weight   |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- |
| Silent drift   | Accepted plan, planned file list, actual diff, commit changes, and PR description                                                                     | The diff follows the accepted plan and stays within scope. No unapproved behavior changes, unrelated features, configuration changes, or refactors are introduced. The PR description accurately reflects the actual diff.                       | Required |
| Not tested     | Plan test commands and expected results, `test_evidence.md`, actual test output, regression tests, control cases, and repository testing requirements | Relevant tests and reproduction steps are run and supported by evidence. Required behavior and controls are adequately validated. Required tests are not skipped, and claimed results are not accepted without supporting evidence.              | Required |
| Unreviewable   | Diff hunks, changed-file list, comments, temporary debugging statements, unused helpers, abandoned implementations, and unrelated edits               | The diff is focused, understandable, and ready for review. No unnecessary refactors, debug code, unused helpers, abandoned code, or unrelated changes make the intended fix difficult to review.                                                 | Required |
| Standards wall | Repository PR template, contribution guide, issue thread, required checklists, documentation and changelog requirements, and PR description           | Applicable repository requirements are satisfied. Required issue references, checklists, documentation, changelog entries, formatting, tests, and contribution policies are addressed accurately. No required submission convention is violated. | Required |

## Verdict rules

* **Accept:** All four required checks pass.
* **Reject:** Any required check fails or is unclear.
* Assign `pass` only when evidence supports the pass condition.
* Assign `fail` when evidence demonstrates a violation.
* Assign `unclear` when the available evidence is insufficient to establish compliance.
* Do not invent requirements or test results.
* An honestly disclosed limitation does not automatically require rejection; evaluate it against the accepted plan and repository requirements.
* Missing evidence for a required condition is not a pass.

## Grading discipline

1. Compare the actual diff with the accepted plan before grading silent drift.
2. Verify test evidence against the planned behavior and repository testing requirements.
3. Inspect the diff for unrelated changes, debugging remnants, unused code, and abandoned experiments.
4. Check repository-specific submission requirements before grading standards wall.
5. Provide one concise, evidence-based explanation for each check.
6. Apply the same four checks and verdict rules to every evaluation package.