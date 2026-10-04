# Procedure: how this skill grades a plan package

## 1. Read order

1. Read `scope.md` in live mode and confirm that the issue is inside the scoped source. Record any repository-specific house rules that affect grading.
2. Read `rubric.md` in both live and eval modes.
3. Read `references/evidence-guide.md` in both live and eval modes.
4. List the rubric checks and record the verdict rule before grading the candidate plan.
5. Read the entire package before assigning any check a grade.
6. Read the **Issue context** first. Record the reported problem, expected behavior, affected environment, and relevant constraints.
7. Read the **Candidate plan/comment** next. Record the candidate's diagnosis, scope, proposed files or code areas, approach, tests, risks, uncertainties, and communication claims. Do not treat these claims as proven yet.
8. Read the **Repro report** next. Record the exact reproduction steps, observed failure, control runs, logs, measurements, and other artifacts that establish what the evidence actually demonstrates.
9. Read the **Thread highlights** and **Repo facts** when present. Record maintainer findings, prior art, repository constraints, contribution rules, testing requirements, and AI-use or comment policies.
10. Use the Issue and Repro evidence to establish what the plan must explain before judging whether the Candidate plan explains it correctly.

## 2. Evidence gathering

1. For each rubric check, identify the evidence named by that check before assigning a grade.
2. Use `references/evidence-guide.md` to locate the evidence for each check.
3. For **diagnosis**, gather the Issue, Repro evidence, Thread highlights, and relevant Repo facts. Record the demonstrated cause, the behavior it must explain, and any stronger maintainer finding.
4. For **scope**, gather the Candidate plan's proposed changes and compare them with the demonstrated problem, diagnosis, and Thread direction. Record any necessary supporting work and any unrelated additions.
5. For **file**, gather the Candidate plan's named files, functions, modules, or code areas and compare them with the Repo facts and demonstrated cause.
6. For **approach**, gather the proposed implementation steps and compare them with the diagnosis, scope, repository structure, and maintainer findings.
7. For **test_plan**, gather the original reproduction, control cases, proposed tests, and expected results. Record whether the proposed tests can demonstrate an observable successful outcome.
8. For **risks**, gather the proposed change, affected code paths, Repo facts, Repro evidence, and known platform or compatibility constraints. Record material risks introduced by the change.
9. For **evidence_uncertainty**, gather the plan's technical claims, assumptions, open questions, and risks. Compare each important claim with the available Issue, Repro, Thread, and Repo evidence.
10. For **comment_conventions**, gather the Candidate plan comment, Thread highlights, repository contribution guidance, issue templates, and AI-use requirements.
11. In live mode, gather issue-side evidence from the exact GitHub issue, reproduction comment, issue thread, repository documentation, and draft locations named by the evidence guide.
12. In eval mode, use the relevant sections and lines from the package bundle. Record the smallest evidence needed to support each grade.
13. Do not use the Candidate plan itself as proof that its diagnosis or technical claims are correct. Candidate claims must be compared with independent package evidence.
14. If required evidence is genuinely absent, record that it is absent rather than inventing or inferring it.

## 3. Check execution

1. Grade each rubric check independently as `pass`, `fail`, or `unclear`.
2. For every grade, record one concise evidence fact or quote that supports the decision.
3. Grade **diagnosis** by comparing the candidate's stated cause with the strongest available Issue, Repro, and Thread evidence. Pass only when the cause is supported by the evidence.
4. Grade **scope** by checking whether the proposed work is limited to the demonstrated problem and necessary supporting changes. Fail unrelated redesign, new features, speculative cleanup, or unsupported broad changes.
5. Grade **file** by checking whether the plan identifies relevant implementation locations that allow a developer to start. Fail when locations are missing, unrelated, or too vague to execute.
6. Grade **approach** by checking whether the proposed implementation directly addresses the demonstrated cause and is technically plausible. Fail an investigation-only plan when it does not provide a concrete implementation direction.
7. Grade **test_plan** by checking whether tests reproduce the triggering condition and verify a concrete observable successful result. Also check relevant controls, regression cases, compatibility cases, or performance checks when material.
8. Grade **risks** by checking whether material compatibility, regression, performance, data-loss, API, shared-code-path, or platform risks introduced by the proposed change are considered.
9. Grade **evidence_uncertainty** by checking whether confirmed facts are separated from hypotheses and unresolved questions. Fail unsupported certainty or claims that contradict stronger evidence.
10. Grade **comment_conventions** by checking whether the proposed comment follows the issue discussion, repository contribution rules, templates, and required AI disclosure.
11. If a check's required evidence is genuinely absent, grade it `unclear` rather than guessing.
12. Treat `unclear` as a failing outcome for the final verdict because every rubric check is required.
13. Do not compensate for one failed check with strong results on another check. Each required check must pass independently.
14. In live mode, also compare the draft communication with `voice-guide.md` and record any broken voice rule when the workflow requires it.
15. A valid workaround may pass when the evidence demonstrates that it works, the scope supports documenting it, and the plan does not falsely claim that the underlying bug has been fixed

### Missing evidence rule

When evidence required for a check is genuinely absent:

1. Do not invent or infer the missing fact.
2. Check whether the candidate explicitly identifies the uncertainty.
3. If the missing evidence prevents the check from establishing that the plan is ready, grade the check **fail/unclear** according to the rubric.
4. Because every check is required, an unclear required check results in **REJECT**.

## 4. Verdict assembly

1. Collect the final result for all rubric checks.
2. Apply the verdict rule from `rubric.md` exactly as written.
3. Return **ACCEPT** only when every required check is `pass`.
4. Return **REJECT** when any required check is `fail` or `unclear`.
5. Do not average the check results or use a "mostly pass" result.
6. If the verdict is **REJECT**, identify the check or checks that caused the rejection.
7. For each deciding failure, state the evidence fact or quote that demonstrates why the candidate plan does not satisfy that check.
8. If the verdict is **ACCEPT**, confirm that all required checks passed.
9. In live mode, include any applicable `voice-guide.md` violations in the summary.
10. Output the result using the required evaluation format.
11. The final verdict must always be binary: **ACCEPT — ready to post and build from**, or **REJECT — hold and revise**.

