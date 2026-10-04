# Rubric: is this plan ready to post and build from?

## Checks

| Check                | Evidence                                                                                                                                          | Pass condition                                                                                                                                                                                                                                                                                                                                           | Weight   |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| diagnosis            | The plan's stated cause read against the Issue, Repro evidence, Thread highlights, control runs, and relevant maintainer findings.                | The diagnosis identifies the demonstrated underlying cause and agrees with the strongest available evidence. Fail if it only restates the symptom, contradicts stronger evidence, or presents an unverified hypothesis as confirmed.                                                                                                                     | required |
| scope                | The plan's scope and proposed changes read against the diagnosis, Repro evidence, and Thread highlights.                                          | The proposed work is limited to the demonstrated problem and necessary supporting changes. Fail if it adds unrelated improvements, broad redesigns, new features, or speculative cleanup that the evidence does not require.                                                                                                                             | required |
| file                 | The proposed files, functions, modules, or code locations read against the diagnosis, Repo facts, and Approach.                                   | The plan identifies relevant implementation locations that a developer can use to begin the fix. Fail if locations are missing, unrelated, or deferred so broadly that implementation cannot begin.                                                                                                                                                      | required |
| approach             | The proposed implementation read against the diagnosis, scope, Repo facts, Thread highlights, and known constraints.                              | The approach directly addresses the demonstrated cause, is technically plausible, and is specific enough to implement. Fail if it only hides the symptom, consists mainly of investigation, or depends on an unsupported architectural assumption.                                                                                                       | required |
| test_plan            | The proposed tests read against the Repro evidence, control runs, expected behavior, and any relevant regression cases.                           | Tests reproduce the original failure and verify a concrete observable successful outcome after the change. Relevant controls, regression cases, compatibility cases, or performance checks are included when material. Fail if success is described only as "feels better", "looks faster", "doesn't break anything", or another non-observable outcome. | required |
| risks                | The proposed changes read against the Repro evidence, Repo facts, Thread highlights, and affected code paths.                                     | Material compatibility, regression, performance, data-loss, API, shared-code-path, or behavioral risks introduced by the proposed change are considered where relevant. Fail if a clear significant risk is ignored.                                                                                                                                     | required |
| evidence_uncertainty | The plan's diagnosis, technical claims, assumptions, and unresolved questions read against all available Issue, Repro, Thread, and Repo evidence. | Confirmed facts are distinguished from hypotheses, and important unknowns are explicitly identified. Fail if speculation is presented as fact, stronger contradictory evidence is ignored, or the plan claims a broader root cause than the evidence establishes.                                                                                        | required |
| comment_conventions  | The Candidate plan comment read against Thread highlights, Repo facts, contribution guidelines, issue templates, and any stated AI policy.        | The comment accurately describes the proposed work, follows repository-specific contribution conventions, respects prior discussion, and makes any required AI disclosure. Fail if it omits a required disclosure, contradicts repository policy, makes unsupported commitments, or claims certainty not supported by the evidence.                      | required |

## Verdict rule

Accept (ready) if every required check passes.

Reject (hold) if any required check fails or is unclear.

The final verdict must be binary: accept or reject.

## Important interpretation rules

### 1. Symptom is not diagnosis

A plan must explain the demonstrated cause, not merely repeat what is slow, broken, missing, or crashing.

For example:

* "git_status takes 1.9 seconds" is evidence of a performance problem, not a root-cause diagnosis.
* "the cached pointer becomes stale after page capacity changes" is a diagnosis when the reproduction and thread evidence support it.

### 2. Investigation plans are not implementation-ready plans

Plans such as:

* "profile it and see what is slow"
* "investigate which layer is responsible"
* "try different protocols"
* "optimize whatever profiling finds"

should fail `diagnosis`, `approach`, and usually `file` unless the evidence already establishes enough detail for implementation.

Investigation can be included as a small implementation step, but it cannot replace the core fix.

### 3. Do not reward unsupported redesign

A plan should not receive credit merely because a larger architectural solution sounds technically sophisticated.

Fail `scope` when a narrow reproduced bug is used to justify unrelated work such as:

* replacing an entire dependency
* introducing a new public option
* redesigning an architecture
* migrating unrelated components
* rewriting a subsystem
* broad cleanup
* adding unrelated retry, caching, or UI behavior

unless the evidence demonstrates that the additional work is necessary for the reported problem.

### 4. Tests must prove an observable result

A test plan should connect:

**reproduction → change → expected observable result**

Good examples include:

* the failing command no longer exits with the error
* the leaked class is absent after the transition
* the viewport no longer jumps to the wrong row
* the regression test passes
* the timeout change succeeds on the reproduced slow connection

Weak acceptance criteria include:

* "should feel faster"
* "nothing else should feel broken"
* "looks much better"
* "should not panic" without checking the expected analyzer result

### 5. Repository policy is part of readiness

Repository-specific rules override generic expectations.

If the repository requires:

* AI disclosure
* human review
* specific issue/PR wording
* particular testing requirements
* contributor acknowledgements

the Candidate plan comment must comply.

A technically excellent plan can still be **Reject** if its proposed comment violates an explicit repository convention.

### 6. Uncertainty must be explicit

A plan may pass while containing uncertainty when the uncertainty is honestly identified and bounded.

For example:

> "I have not yet verified which layer clamps the viewport; I will confirm this during implementation."

This is acceptable when the rest of the plan is evidence-based.

By contrast:

> "The reattach path wires stdin before OSC responses are consumed."

should fail if the supplied evidence has not established that mechanism.

### 7. Existing thread evidence has priority

When a maintainer or contributor has already established a cause, the candidate plan must account for it.

Fail the diagnosis if the candidate replaces a supported explanation with an unsupported one.

### 8. A workaround can be a valid approach

The plan does not always have to fix the deepest underlying implementation if the issue and evidence support a bounded workaround.

A documentation/workaround plan can pass when:

* the workaround is demonstrated to work,
* the limitation is clearly described,
* the plan does not falsely claim to fix the underlying bug,
* and the issue's scope supports documenting the workaround.

## Required final verdict

The evaluator must return exactly one of:

* **ACCEPT** — all required checks pass.
* **REJECT** — at least one required check fails or is unclear.





