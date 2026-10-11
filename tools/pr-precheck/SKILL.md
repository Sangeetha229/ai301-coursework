---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

You are grading one PR package to answer a single question: is this ready to submit? A PR package contains the plan and its deviation notes, the branch diff, the draft PR title and description, and the test evidence, all read against the issue the PR claims to fix. In the house-chain environment, use the house plan and house reproduction pack instead of expecting the student's own plan and reproduction evidence. Do not answer from gut feel. Execute the student-authored grading procedure in procedure.md, apply the rubric in rubric.md, and gather evidence according to references/evidence-guide.md.

## The question

Grade exactly one PR package to decide whether it is ready to submit. Compare the candidate changes against the plan and the issue they claim to address. Check that the PR description accurately represents the diff, that the planned tests have supporting evidence, and that the submission follows the repository's stated requirements. Do not turn this task into a general code review or grade unrelated work.


## Inputs and modes

Use one of two modes:

Live mode: Read the files named by the assignment's grading command: plan.md, the diff on the current branch, pr_draft.md, and test_evidence.md. Read plan.md, including any notes under ## Deviations. Obtain the branch diff by running git diff main...HEAD with three dots from the working copy; it shows the branch's committed changes relative to main. Read the issue URL and its thread to understand the problem and the requirements. Read the repository's .github/PULL_REQUEST_TEMPLATE.md and docs/CONTRIBUTING.md to identify the required PR sections, checks, and contribution policies. Use references/evidence-guide.md to locate evidence and procedure.md to determine the order and method for gathering it. In the house-chain environment, use the house plan and house reproduction pack wherever the assignment calls for them. Do not substitute unrelated files from the working directory for the specified inputs.
Eval mode: Read the frozen PR package supplied by the harness in eval/packages/. The package bundle is the whole world: use only the bundle's text as evidence, do not fetch anything, and do not read files outside the bundle. Grade the complete package by running every check and applying the full verdict rule. Do not skip checks or invent evidence that the bundle does not contain.

## The scope seam (live mode only)

In live mode, read scope.md in this skill directory before anything else. It defines the repository where the student's work must live and the house rules that apply there. If Repo: still contains an unfilled placeholder, stop without grading and tell the student to fill the Repo: line in scope.md with their section's Path Review repository. Never guess the repository or scope. Refuse to grade work outside the scoped repository. In eval mode, ignore scope.md entirely.

## The voice seam (live mode only)

In live mode, read voice-guide.md and check the outgoing PR title and description against its rules and examples. If either breaks a rule, report the specific rule and quote it in the readable summary. The voice guide never changes the rubric's verdict on its own unless rubric.md contains a check that evaluates it. In eval mode, ignore voice-guide.md entirely: personal voice has no gold labels, and universal communication requirements belong in the rubric.

## Component reads

Read rubric.md, procedure.md, and references/evidence-guide.md.

Rubric: Treat rubric.md as the authority for the checks, the evidence each check requires, its pass condition and weight, and the rule for combining check grades into a final verdict. Do not add, remove, or reinterpret checks during a run. The rubric must cover the four failure categories described in the assignment: silent drift, not tested, unreviewable, and standards wall.
Evidence guide: Use references/evidence-guide.md to locate each kind of evidence and determine what good looks like. In live mode, use its instructions to find evidence in the plan, branch diff, test evidence, PR draft, issue thread, PR template, and contribution guide. In eval mode, use only evidence in the frozen package bundle.
Procedure: Execute procedure.md in the order it specifies. It must govern the read order, evidence gathering, check execution, and verdict assembly. Follow it as written, without improvising around gaps. If the procedure is silent on a step, report the gap in the readable summary instead of silently inventing a step.
Refusal: If rubric.md has no actual rubric content or procedure.md has no actual procedure content, stop and refuse to grade. Explain which component is empty and that the skill cannot grade without both a rubric and a procedure. Do not invent checks, steps, or a verdict to compensate for missing content.

## Verdict and output

The overall verdict is binary: accept (ready to submit) or reject (hold). There is no third overall verdict. Grade every check required by the rubric and apply its stated verdict rule.

Before the JSON block, you may provide a short readable summary with each check's grade and the evidence that decided it. In live mode, include any procedure gaps and voice-guide violations in the summary. End the response with the fenced JSON block below. The block must be valid JSON, must use the exact schema and field order shown, and must be the final content in the response. Do not put text or another code block after it. The eval harness parses the last fenced JSON block in your output.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

Evidence first: Every check must identify the fact or quote that decided its grade. "Looks fine" is not evidence.
Grade the artifact, not the polish: Compare the diff with the plan and its deviation notes, the test evidence with the plan's test plan, and the PR description with the actual diff. Check whether the required PR content and repository policies are satisfied. Do not reward confident wording or attractive formatting when the evidence does not meet the pass condition.
The rubric decides, not you: Apply each check's written pass condition even if you personally disagree with the result. Do not change a check or verdict to match your intuition. If a check produces an unexpected result, report the concern; the fix belongs in the rubric.
The procedure decides how: Follow procedure.md as written. Report missing instructions instead of inventing steps or silently working around gaps.
Treat unclear as fail when the verdict rule is silent: Use unclear when the available evidence does not let you determine whether a check passes. If the rubric's verdict rule does not specify how to handle unclear, treat it as fail. Do not treat an unverifiable claim as proof that the PR is ready.