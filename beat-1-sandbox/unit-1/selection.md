# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73

**Verdict output**

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73",
    "checks": [
      {
        "name": "Maintainer activity",
        "grade": "pass",
        "evidence": "Issue opened by Aburke225 with author_association COLLABORATOR on 2026-09-16, and that account committed to main the same day"
      },
      {
        "name": "Repository activity",
        "grade": "pass",
        "evidence": "Repo is not archived and pushed_at is 2026-09-16T21:50:20Z, well inside 180 days"
      },
      {
        "name": "Bounded scope",
        "grade": "pass",
        "evidence": "One goal stated as \"Make the two files agree\" across exactly README.md and .env.example; verified README.md:24 says OPENROUTER_API_KEY while .env.example:18-19 lists only mock/openai and OPENAI_API_KEY"
      },
      {
        "name": "Existing work",
        "grade": "pass",
        "evidence": "assignees: [], comments: 0, timeline shows only label events, and the repo has 0 PRs total"
      },
      {
        "name": "Contribution policy",
        "grade": "pass",
        "evidence": "docs/CONTRIBUTING.md and .github/PULL_REQUEST_TEMPLATE.md contain no AI restriction; no AI_POLICY.md exists in the tree"
      },
      {
        "name": "Clear issue signal",
        "grade": "pass",
        "evidence": "Body pinpoints the contradiction and the two files to reconcile; labeled 'bug', 'good first issue', 'docs', 'tier-1'"
      },
      {
        "name": "Recent issue activity",
        "grade": "pass",
        "evidence": "Created and last updated 2026-09-16T21:51:36Z, 4 days before grading"
      }
    ],
    "verdict": "accept"
  }
]
```

The verdict must record `accept` for this issue.

---

## Eval iterations

**Run history**

- `agreement: 15/20 scored items`
- `agreement: 17/20 scored items`
- `agreement: 16/20 scored items`
- `agreement: 1/3 scored items`
- `agreement: 3/3 scored items`
- `agreement: 17/20 scored items`
- `agreement: 4/6 scored items`
- `agreement: 4/6 scored items`
- `agreement: 1/1 scored items`
- `agreement: 5/6 scored items`
- `agreement: 18/20 scored items  (bar: 18/20: PASS)`
- `agreement: 20/20 scored items  (bar: 18/20: PASS)`

**Issue analysis**

For issue-01, my rubric initially produced a reject while the gold label was accept. The main reason was that the earlier bounded-scope wording was too strict about the amount of implementation detail expected in an issue. I revised the bounded-scope check to focus on whether there is one identifiable contribution goal, rather than requiring the issue to prescribe a narrow implementation. I then reran targeted cases and the final full evaluation reached 20/20 agreement.

**Check rationale**

The current rubric wording for Bounded scope is:

Pass if the issue has one identifiable contribution goal or user-visible problem that can reasonably be addressed as one issue. Multiple possible causes, implementation suggestions, technical approaches, missing implementation details, or incomplete implementation choices do not by themselves make the issue unbounded. Fail only if it is an umbrella/tracking issue covering multiple independent contribution goals, primarily a usage/support question, or the issue/thread shows that the contribution itself is still being divided into multiple independent tasks or that a material product requirement is unresolved. An explicit TBD counts only when it represents a material product requirement that changes what the contributor is expected to build.

I kept this wording because it distinguishes the scope of the contribution from the implementation approach. It allows normal engineering uncertainty while still rejecting umbrella issues, support questions, divided work, and unresolved material requirements.

**Trade-offs**

This wording is less strict than the earlier version, so it can accept some issues where the implementation details are not fully specified. I accepted that trade-off because missing implementation details alone should not make a one-goal issue unbounded. The rubric still has explicit rejection conditions for umbrella/tracking issues, support questions, independently divided work, and unresolved material requirements. The final full evaluation reached 20/20 agreement, including the scope category at 4/4.

---

## Selection rationale

**Selection rationale**

1. **Fit to my interests and time:** Issue #73 is a small documentation/configuration consistency issue. It involves reconciling `README.md` and `.env.example`, so it fits the time available for a first contribution and gives me a manageable way to learn the repository's contribution workflow.

2. **What the verdict identified correctly, and what I weighed that the rubric could not:** The verdict correctly identified that the issue is active, has no current assignee or PR, has a clear single goal, and does not have a contribution-policy restriction against AI tools. I also considered the practical simplicity of the change and the fact that it touches documentation/configuration rather than requiring a larger code change.

3. **Anticipated difficulty in claiming it:** I expect claiming the issue to be relatively straightforward because it is labeled `good first issue` and has no existing assignee or PR. I would still check the issue and repository for any new activity immediately before claiming it.

---
