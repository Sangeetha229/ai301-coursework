# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

**Where it lives**

* **Eval package:** Read the `Plan context` section for the accepted plan, including the scope, files to change, files excluded from changes, approach, test plan, and deviations. Compare these details with the `Candidate PR` title, description, commit list, and diff.
* **Live mode:** Read `plan.md`, especially `Scope`, `Files to Touch`, `Approach`, and `Deviations`. Compare them with the actual branch diff, changed-file summary, commit history, and `pr_draft.md`.
* Check both the files changed and the behavior or documentation changes within those files. Read the PR description alongside the diff to verify that its claims match the implementation.

**What good looks like**

Every changed file and behavior falls within the accepted plan's scope, or a documented deviation explains the difference. The PR description accurately describes what the diff delivers and does not claim changes or verification that the evidence does not support.

For example, in Issue #73, the plan permits changes only to `README.md` and `docs/SETUP.md`. Changes to `core/config.py`, `.env.example`, or the provider implementation would require explicit authorization or a documented, accepted deviation. The PR description must not claim that application code was changed when the diff contains only documentation edits.

## Test evidence (harness category: not-tested)

**Where it lives**

* **Eval package:** Read the accepted plan's `Test plan` and reproduction evidence in `Plan context`. Compare each required check and expected result with the `Test evidence` section under `Candidate PR`. Inspect the reported commands, before-and-after results, and any repository test requirements.
* **Live mode:** Read the `Test Plan` in `plan.md` and compare it with the command output and assessments in `test_evidence.md`. Check the `Testing` section of `pr_draft.md` for consistency with the recorded results. Consult the repository's contribution guide and PR template for additional required checks.
* For each planned check, record the command, expected result, observed result, and whether the evidence supports the conclusion. Distinguish checks that were actually run from checks that are merely planned, unchecked, or explicitly not run.

**What good looks like**

The evidence demonstrates the behavior required by the plan: the relevant command or test was run, the expected result is stated, and the observed output supports the conclusion. A general statement such as "tests pass" is not enough when the required command, output, or expected behavior is missing.

For Issue #73, good evidence includes checking for remaining `OPENROUTER` references, verifying that `OPENAI_API_KEY` remains in `.env.example`, reviewing the documentation diff, and confirming the changed-file summary. The evidence must not claim that unit tests, integration tests, linting, type checking, or CI passed when those checks were not run.

A documentation-only change does not automatically require application tests that are unrelated to the change. Evaluate the verification against the plan and the repository's actual requirements. Missing evidence for a required check must not be treated as a pass.

## Diff quality (harness category: unreviewable)

**Where it lives**

* **Eval package:** Inspect the complete `Candidate PR` diff and commit list. Compare the changed files and hunks with the plan's stated scope and approach.
* **Live mode:** Inspect the actual branch diff, changed-file summary, and commit history. Review the corresponding files in the working copy and compare the result with `plan.md` and `pr_draft.md`.
* Look for debugging statements, commented-out implementations, unused helpers, abandoned code, duplicated content, unrelated refactoring, formatting churn, and drive-by changes. Use the actual diff to establish whether a problem exists.

**What good looks like**

The intended fix is easy to identify in the diff, and the changes are limited to what is needed for the accepted plan. There are no unnecessary edits or leftover debugging artifacts that obscure the implementation or make review harder.

For Issue #73, the diff should show the removal or correction of the unsupported OpenRouter setup instructions in `README.md` and `docs/SETUP.md`. Unrelated application changes or redundant documentation comments should be identified and evaluated for their impact on scope and reviewability. Record the exact file or diff hunk supporting a failure rather than relying on a general impression.

## Standards and comms (harness category: standards-wall)

**Where it lives**

* **Eval package:** Read the `Repo facts` section for the repository's PR template, contribution instructions, and stated policies. Check any applicable requirements in the issue thread and compare them with the candidate PR description and other supplied submission evidence.
* **Live mode:** Inspect the repository's PR template, `CONTRIBUTING.md`, issue thread, and applicable documentation or testing instructions. Compare those requirements with `pr_draft.md`, the actual diff, `test_evidence.md`, and any required submission artifacts.
* Check required issue references, description sections, test checklists, documentation or changelog entries, policy disclosures, and explicit maintainer requests when they apply. Verify that required sections contain accurate information rather than boilerplate or unsupported claims.

**What good looks like**

Every applicable repository requirement is addressed with accurate evidence, and explicit maintainer instructions are not ignored. Required checklists, issue references, documentation, and policy disclosures are present where applicable; unrun checks are not falsely marked as passed.

For Issue #73, the draft references Issue #73, describes the documentation-only change, reports the observed documentation checks, and explicitly states that application tests and CI are not claimed as passed. It also includes an AI-use disclosure. The repository's actual requirements must still be checked before deciding whether these items are sufficient.

Do not invent repository rules. If the package does not establish whether a required item is present or whether a policy applies, record the uncertainty instead of asserting a violation. Compare description claims with the actual diff under **Plan fidelity**, rather than treating every inaccurate implementation claim as a separate communication failure.