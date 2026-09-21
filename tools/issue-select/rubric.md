# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

# Rubric: is this a good first issue?

## Checks

# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|-------|----------|----------------|--------|
| Maintainer activity | Repo facts: last 5 default-branch commits, plus maintainer responses in the issue/comment thread | Pass if there is at least one non-bot default-branch commit within the last 90 days OR a maintainer has responded to an issue within the last 90 days | required |
| Repository activity | Repo facts: last push, latest release, archived status | Pass if the repository is not archived and there has been a push to the repository within the last 180 days | required |
| Bounded scope | Issue body and comment thread | Pass if the issue has one identifiable contribution goal or user-visible problem that can reasonably be addressed as one issue. Multiple possible causes, implementation suggestions, technical approaches, missing implementation details, or incomplete implementation choices do not by themselves make the issue unbounded. Fail only if it is an umbrella/tracking issue covering multiple independent contribution goals, primarily a usage/support question, or the issue/thread shows that the contribution itself is still being divided into multiple independent tasks or that a material product requirement is unresolved. An explicit `TBD` counts only when it represents a material product requirement that changes what the contributor is expected to build. |
| Existing work | Issue assignees, linked PRs, and claim comments in the issue thread | Pass if there is no active assignee, no open linked PR implementing the issue, and no evidence in the thread that the issue is currently occupied by another contributor. Fail if the thread contains a current work claim, repeated claims that remain unresolved, a maintainer or project bot explicitly stating that another contributor is already working on the issue or that the issue cannot be claimed, or an existing contribution attempt that remains unresolved. A single old claim, closed PR, or clearly abandoned attempt alone does not fail this check. |
| Contribution policy | Contribution policy and AI policy files identified in the repo facts | Pass if there is no explicit prohibition on AI-assisted or AI-generated contributions | required |
| Clear issue signal | Issue body and labels | Pass if the issue provides enough information to identify the requested change, such as a clear problem, expected behavior, or acceptance criteria | preferred |
| Recent issue activity | Issue open date, comments, and recent updates | Pass if the issue has meaningful recent activity or a clear current request. Lack of recent activity alone does not reject an issue | preferred |

## Verdict rule

An issue is accepted only when every required check passes.

Preferred checks NEVER affect the accept/reject verdict. A preferred check may be marked fail without causing rejection; preferred checks are used only to rank issues that have already passed all required checks.

If a required check is unclear, treat it as fail.

A short or old issue is not automatically out of scope. Judge bounded scope from the issue body and comment thread based on whether the requested contribution can reasonably be completed as one issue.

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict, they
rank accepted issues; unclear counts as fail." -->
