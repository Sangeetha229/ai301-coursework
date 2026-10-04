# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

### Where it lives

**Eval bundle:**

* **Issue context:** reported symptom, expected behavior, affected environment, and constraints.
* **Repro-evidence block:** exact reproduction steps, observed behavior, control runs, logs, errors, traces, and measurements.
* **Thread highlights:** maintainer-confirmed causes, contributor findings, prior art, accepted workarounds, and unresolved questions.
* **Candidate plan:** stated diagnosis and explanation of the proposed cause.
* **Repo-facts block:** relevant implementation areas that can confirm or contradict the proposed diagnosis.

**Live mode:**

* **GitHub issue description:** reported symptom, expected behavior, environment, and constraints.
* **Issue thread:** maintainer and contributor findings, confirmed causes, prior art, workarounds, and unresolved questions.
* **Posted reproduction comment:** commands, inputs, outputs, logs, measurements, and control runs.
* **Draft plan:** candidate diagnosis and proposed explanation.
* **Repository source and docs:** relevant implementation locations and documented behavior.

### What good looks like

The stated cause explains the behavior demonstrated by the reproduction evidence and agrees with stronger maintainer or thread evidence.

A diagnosis fails when it only repeats the symptom, contradicts stronger evidence, or presents an unsupported hypothesis as a confirmed cause.

The strongest evidence should be preferred over speculation. For example, if logs demonstrate an error in one component, a plan should not blame a different component without evidence supporting that explanation.

---

## Scope

### Where it lives

**Eval bundle:**

* **Candidate plan scope statement:** what the plan proposes to change.
* **Candidate plan file/module list:** implementation areas included in the change.
* **Candidate approach:** supporting changes required by the proposed fix.
* **Issue context and Repro evidence:** the boundaries of the demonstrated problem.
* **Thread highlights:** maintainer-requested scope, prior art, and known constraints.

**Live mode:**

* **Draft plan:** in-scope and out-of-scope statements, proposed files, and implementation steps.
* **Issue description and reproduction comment:** actual problem boundaries.
* **Issue thread:** maintainer direction, prior fixes, related pull requests, and requests for specific work.

### What good looks like

The proposed work is limited to the demonstrated problem and the supporting changes necessary to fix it.

A bounded plan names the relevant files or areas and avoids unrelated redesigns, new features, dependency migrations, speculative cleanup, or broad architectural changes that the evidence does not require.

A narrow bug should normally produce a narrow fix. A broader change is acceptable only when the evidence or repository discussion demonstrates why the broader scope is necessary.

A documented workaround can pass when it is demonstrated to work and the plan clearly describes it as a workaround rather than claiming to fix the underlying bug.

---

## Executability

### Where it lives

**Eval bundle:**

* **Candidate plan:** files, functions, modules, implementation steps, order of work, and proposed changes.
* **Repo-facts block:** actual repository structure, relevant files, functions, existing tests, and architecture.
* **Thread highlights:** known implementation direction, prior art, or maintainer recommendations.
* **Repro-evidence block:** behavior the implementation must address.

**Live mode:**

* **Draft plan:** proposed files, functions, implementation sequence, and change description.
* **Repository source:** actual locations of the code and tests.
* **Issue thread:** maintainer guidance and prior art.
* **Repository documentation:** architecture, contribution, and testing guidance.

### What good looks like

A developer who did not write the plan can identify where to start, what to change, and how the proposed change addresses the demonstrated cause without first having to rediscover the entire problem.

A good plan identifies relevant files, functions, modules, or bounded code areas and gives a technically plausible implementation approach.

A plan fails executability when it only says to:

* investigate;
* profile and see what happens;
* try different approaches;
* optimize whatever profiling finds;
* fix the issue once the cause is known.

Investigation may be a small step, but it cannot replace the core implementation direction when the available evidence already supports one.

---

## Test plan

### Where it lives

**Eval bundle:**

* **Repro-evidence block:** original failing command, input, output, controls, logs, and measurements.
* **Candidate plan test section:** proposed tests, expected results, regression cases, and compatibility checks.
* **Repo-facts block:** existing test locations and relevant test conventions.
* **Thread highlights:** known regression tests, prior test expectations, or maintainer-requested cases.

**Live mode:**

* **Posted reproduction comment:** exact failure reproduction and control runs.
* **Draft plan:** proposed regression tests and expected results.
* **Repository test suite:** existing tests covering the affected behavior.
* **Issue thread:** prior test cases and maintainer-requested verification.

### What good looks like

A decisive test plan connects:

**original reproduction → proposed change → observable successful result**

The test plan should reproduce the original failure or the same triggering condition and then verify a concrete expected outcome after the change.

When relevant, it should also preserve successful control cases and test compatibility, regression, platform, or performance behavior.

Good evidence names observable results such as:

* the command exits successfully;
* the expected file is produced;
* the panic no longer occurs;
* the expected notice appears;
* the expected class is removed;
* the measured operation stays below a defined threshold;
* the correct parser or analyzer result is produced.

Vague statements such as "should feel faster," "looks better," "doesn't break anything," or "should not crash" are not decisive unless the plan defines how that outcome will be observed.

---

## Honesty

### Where it lives

**Eval bundle:**

* **Candidate plan diagnosis:** technical claims about the cause.
* **Candidate plan approach:** assumptions about implementation behavior.
* **Candidate plan risks:** compatibility, performance, regression, and other concerns.
* **Candidate plan uncertainty/open questions:** unresolved implementation details.
* **Repro evidence and Thread highlights:** evidence that confirms or contradicts those claims.
* **Repo facts:** constraints that may limit the proposed approach.

**Live mode:**

* **Draft plan:** assumptions, open questions, risks, and proposed deviations.
* **Issue thread:** unresolved questions, maintainer corrections, and new findings.
* **Reproduction comment:** facts demonstrated by testing.
* **Repository source/docs:** facts that can confirm or contradict assumptions.

### What good looks like

The plan clearly distinguishes confirmed facts from hypotheses and unresolved questions.

A plan may contain uncertainty when it states the uncertainty honestly and bounds its impact. For example, an exact implementation detail may still need confirmation even when the overall failure mechanism is established.

Fail when speculation is presented as fact, stronger contradictory evidence is ignored, or the plan claims a broader root cause than the evidence establishes.

During implementation, a material change from the planned approach should be recorded as a deviation with the reason and supporting evidence rather than silently replacing the original diagnosis.

Risks should address material consequences of the proposed change, such as compatibility, regression, performance, shared code paths, platform behavior, data loss, or API behavior.

Do not require speculative risks that have no connection to the proposed change.

---

## Comms

### Where it lives

**Eval bundle:**

* **Candidate plan comment:** proposed issue comment or maintainer-facing communication.
* **Thread highlights:** existing discussion, maintainer direction, prior art, and commitments.
* **Repo-facts block:** contribution guidelines, issue templates, comment conventions, and AI-use policies.
* **Candidate plan:** technical claims and commitments made in the proposed comment.

**Live mode:**

* **GitHub issue thread:** existing maintainer and contributor discussion.
* **Draft plan/comment:** proposed communication.
* **Repository CONTRIBUTING documentation:** contribution rules and communication requirements.
* **Issue templates:** required information or wording.
* **Repository AI policy:** required AI disclosure or review requirements.

### What good looks like

The plan comment accurately describes the proposed work, reflects the issue discussion, and follows repository-specific communication requirements.

A thread-aware comment:

* acknowledges relevant maintainer findings;
* does not contradict established discussion;
* does not claim an unverified cause as confirmed;
* does not promise work outside the proposed scope;
* follows contribution and issue-comment conventions;
* includes required AI-use disclosure when the repository requires it.

A comment fails when it is generic boilerplate that ignores important thread evidence, makes unsupported commitments, omits required disclosures, or claims certainty that the evidence does not support.

Repository-specific rules take priority over generic communication preferences.



