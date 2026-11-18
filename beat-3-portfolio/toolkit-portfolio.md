# Toolkit Portfolio

Path: `beat-3-portfolio/toolkit-portfolio.md`

I built this toolkit to support the full open-source contribution process, from finding a suitable issue to monitoring a pull request after submission. I selected Issue-Scout, PR-Precheck, Ship-Gate, and PR-Monitor as the featured tools because they represent four important decisions in that process: which issue is worth taking, whether the contribution package is ready, whether the work has passed the required checks, and what to do after a pull request is opened. The other tools support individual steps in the workflow.

## Featured Tools

### Issue-Scout

**What it is, and the judgment hole I wrote:**

Issue-Scout searches public GitHub repositories for beginner-accessible contribution opportunities. It uses a search strategy and evaluation rubric to identify issues that are suitable for an outside contributor. It ranks candidates and evaluates them against required checks, stopping when it reaches five accepted issues or fifteen fully evaluated candidates.

The judgment hole I wanted to address was choosing an issue based only on its title, labels, or apparent simplicity. An issue marked `good first issue` is not necessarily available, understandable, or appropriate for an outside contributor. I wanted the tool to check whether the issue had a clear goal, whether the repository welcomed outside contributions, whether the issue was written clearly enough to understand, and whether someone else was already working on it.

**How it evolved, dated:**

- **September 21, 2026:** I created the initial issue-selection rubric. I refined it after recognizing that requiring an issue to describe the exact implementation would reject useful issues. The goal should be identifiable, but the issue does not need to prescribe every implementation detail.
- **November 1, 2026:** I organized the skill with its `SKILL.md`, scope guidance, and evidence guide so that the search and evaluation process had clearer instructions.
- **November 7, 2026:** I revised the rubric and search strategy. I changed the scope check from a strict bounded-scope requirement to a scope-feasibility judgment. An open design detail or a reference to several files should affect the difficulty assessment rather than automatically disqualify an issue.
- **November 7, 2026:** I added a required issue-language check. The title, substantive description, and essential requirements must be understandable in English.
- **November 7, 2026:** I strengthened the contribution-policy check. The repository must explicitly invite outside contributions or provide credible evidence that outside contributors have had pull requests accepted. Unclear contribution policies should not automatically pass.
- **November 7, 2026:** I separated the difficulty assessment from the accept/reject decision and retained a limit of fifteen fully evaluated candidates.
- **November 7, 2026:** I used the tool's issue-selection process to identify `oscarbol09/jvm-mcp#278`, concerning hardcoded TLS 1.2 in the Actuator client and probe, as a contribution opportunity.

**Evidence:**

The search process produced a broad candidate set and then applied the evaluation rules. One scan report recorded:

```text
It reached the 15-full-evaluation limit and accepted three ...
11 additional candidates failed language screening.
A previously accepted issue, rdf-core#228, dropped out ...
Reject: kogn-io/rdf-core#228 is already being fixed.
```

The rubric also changed in meaningful ways:

- **Before:** An issue had to have a tightly bounded scope.
- **After:** The issue needed a feasible, identifiable goal. Open implementation details could influence the difficulty note without automatically causing rejection.
- **Before:** The contribution-policy check could pass without clear evidence that outside contributions were welcome.
- **After:** The rubric required an explicit invitation or credible evidence of accepted contributions from outside contributors.
- **Added:** Issue language became a required check rather than an informal consideration.

The broader search work recorded 641 unique candidates and five accepted issues. I selected `oscarbol09/jvm-mcp#278`, which was ranked seventh in the relevant candidate results and appeared to be a focused bug fix.

### PR-Precheck

**What it is, and the judgment hole I wrote:**

PR-Precheck evaluates a proposed contribution package before a pull request is submitted. It checks whether the work follows the plan and issue, whether the change was tested, whether reviewers can understand the change, and whether repository standards have been followed.

I wrote this tool to address the judgment gap between completing code and having a contribution that is actually ready for review. Passing tests alone does not prove that a pull request is complete. The implementation might drift from the plan, lack adequate test evidence, be difficult to review, or fail to follow the repository's contribution requirements.

Its rubric includes four important areas:

- **Silent drift:** Does the implementation still match the issue and plan?
- **Not tested:** Is there sufficient evidence that the change was tested?
- **Unreviewable:** Can a maintainer understand and review the change?
- **Standards wall:** Does the contribution follow the repository's conventions and requirements?

**How it evolved, dated:**

- **October 11, 2026:** I created the PR-Precheck rubric and procedure and evaluated the initial version.
- **October 11, 2026:** The evaluation recorded a 20/20 result for the tested version.
- **November 8, 2026:** I expanded the scope from pull requests against the course's Path Review repository to pull requests against any public repository. This changed the tool from checking only the rules of one course repository to considering the standards of the repository receiving the contribution.
- **November 8, 2026:** I ran PR-Precheck against the real `jvm-mcp` pull request. It initially failed the Standards wall because the pull request description did not include the sections required by the repository's pull request template.
- **November 8, 2026:** I revised the pull request description to include the required template sections, testing information, the issue reference, and an AI-use disclosure. The next check passed.

**Evidence:**

The real pull request exposed a problem that a generic checklist could miss:

```text
## 2026-11-08 20:26 [manual] oscarbol09/jvm-mcp @ fix/278-actuator-tls-negotiation
  - Standards wall [fail]: Description ignores .github/PULL_REQUEST_TEMPLATE.md: no
    Summary/Changes/Motivation & Architecture Context sections and the Testing &
    Verification checklist is absent, though Fixes #278 and the AI disclosure are present.

## 2026-11-08 20:33 [manual]
  - Standards wall [pass]: PR template sections are filled, 'Fixes #278' is present,
    an AI-use disclosure is included, the commit follows Conventional Commits, and no
    README update is required.
```

This experience showed why repository-specific requirements matter. A pull request can contain a correct implementation and passing tests but still be incomplete because its description does not follow the project's own template.

### Ship-Gate

**What it is, and the judgment hole I wrote:**

Ship-Gate is a pre-submission gate that runs before a `gh pr create` command. It checks the configured repository requirements and PR-Precheck result before allowing the pull request creation step to proceed.

I wrote it to address the gap between knowing what should be checked and actually enforcing those checks. A checklist can be ignored or forgotten. A gate makes the required verification part of the workflow instead of relying entirely on memory.

For the `jvm-mcp` contribution, I configured the manifest to run the repository's formatting check and full Maven verification. I used the repository's contribution instructions to choose the checks rather than assuming that a generic test command would be sufficient.

**How it evolved, dated:**

- **November 8, 2026:** I configured Ship-Gate with the repository-specific checks and a PR creation requirement.
- **November 8, 2026, 16:29:** The hook blocked the PR creation attempt because the required PR information was not ready.
- **November 8, 2026, 20:15:** The hook blocked another attempt because the manifest still contained template values.
- **November 8, 2026, 20:20:** The formatting check failed because the manifest used a command that was not recognized in the Windows environment.
- **November 8, 2026, 20:26:** I corrected the Maven Wrapper command for the Windows environment. Spotless and the full Maven verification then completed successfully.
- **November 8, 2026, 20:26:** The repository checks passed, but PR-Precheck still blocked the submission because the pull request description did not yet satisfy the repository template.
- **November 8, 2026, 20:33:** After correcting the PR description, the gate returned a passing result.
- **November 8, 2026, 20:52:** The hook allowed the pull request creation workflow to proceed, and PR #286 was opened.

**Evidence:**

The Windows command problem was a practical example of why the manifest must match the environment in which it runs:

```text
## 2026-11-08 20:20 [manual]
- verdict: BLOCK (block)
  - Code formatting validation: ./mvnw spotless:check -> FAILED (exit 1, 0s)
    '.' is not recognized as an internal or external command,

## 2026-11-08 20:26 [manual]
  - Code formatting validation: .\mvnw\.cmd spotless:check -> ok (10s)
  - Full build and test verification: .\mvnw\.cmd clean verify -> ok (170s)

## 2026-11-08 20:33 [manual]
- verdict: PASS
```

The command was changed from the Unix-style Maven Wrapper invocation to the Windows-compatible invocation:

```text
Before: ./mvnw spotless:check
After:  .\mvnw\.cmd spotless:check
```

Ship-Gate also demonstrated that passing repository checks did not automatically mean the pull request was ready. PR-Precheck remained a separate requirement and correctly blocked submission until the description was fixed.

**PR-Monitor**

**What it is, and the judgment hole I wrote:**

PR-Monitor reads the state of an existing GitHub pull request and recommends the next appropriate action. It checks review status, comments, CI checks, mergeability, and time since meaningful activity. It uses a playbook to recommend whether to wait, respond to feedback, follow up, or complete the close-out process.

The judgment hole I wanted to address was interpreting a quiet pull request as a reason to immediately contact the maintainer. A lack of comments does not necessarily mean that a pull request has been ignored. Similarly, a mergeable pull request is not necessarily approved. PR-Monitor helps distinguish these states and recommends an action based on evidence rather than impatience.

I created a playbook with response rules, a three-calendar-day staleness threshold, follow-up etiquette, and criteria for walking away from a contribution. I used it to monitor my contribution to `oscarbol09/jvm-mcp`, PR #286.

**How it evolved, dated:**

- **November 8, 2026:** I began using PR-Monitor with PR #286. The initial report recommended waiting because the staleness threshold had not been reached.
- **November 9, 2026:** PR #286 was opened against `main` in the upstream repository from my fork branch, `Sangeetha229:fix/278-actuator-tls-negotiation`. The monitor showed that it was mergeable but had no review decision. I treated mergeability as a technical state, not evidence of maintainer approval.
- **November 13, 2026:** CI failed in the Security & Secret Scan job because Semgrep flagged the generic `"TLS"` algorithm in both production classes. I changed both classes to use `SSLContext.getInstance("TLSv1.3")`, updated the corresponding `ActuatorProbeTest` expectation, ran `./mvnw spotless:apply`, and pushed the revision as `ff484ee`.
- **November 14, 2026, 13:59:** PR-Monitor detected that changes had been requested two days earlier. It recommended waiting rather than sending a reminder because the three-day staleness threshold had not been reached.
- **November 14, 2026:** The maintainer confirmed that the revised implementation resolved the SAST finding, supported TLS 1.3 negotiation with TLS 1.2 fallback on JDK 21+, and passed the TLS protocol integration tests. The maintainer approved and merged the pull request.
- **November 14, 2026:** The final PR-Monitor check recorded `APPROVED`, four passing checks, four comments/reviews, and `MERGED`. It recommended the merged-PR close-out action.

**Evidence:**

[PR #286 — Allow TLS protocol negotiation for insecure Actuator connections](https://github.com/oscarbol09/jvm-mcp/pull/286)

The CI failure identified the following blocking Semgrep finding:

```text
Security & Secret Scan — Semgrep SAST

core/src/main/java/dev/jvmmcp/core/spring/ActuatorClient.java:162
core/src/main/java/dev/jvmmcp/core/spring/ActuatorProbe.java:69

java.lang.security.audit.weak-ssl-context.weak-ssl-context (Blocking)

An insecure SSL context was detected. TLS versions 1.0, 1.1,
and all SSL versions are considered weak encryption.
```

The correction was:

```java
// Before
SSLContext.getInstance("TLS");

// After
SSLContext.getInstance("TLSv1.3");
```

On JDK 21, the TLS 1.3 context enables TLS 1.3 and TLS 1.2 by default. This allows TLS 1.3 negotiation while preserving TLS 1.2 fallback. I made this change in both `ActuatorClient.java` and `ActuatorProbe.java` and updated the corresponding test expectation in `ActuatorProbeTest`.

The maintainer's final review confirmed the result:

> Verified the update to `TLSv1.3`. It correctly allows negotiation of TLS 1.3 while maintaining fallback to TLS 1.2 on JDK 21+, cleanly resolves the SAST check, and passes the TLS 1.2/1.3 matrix integration tests. CI is fully green across all runners. Approving and merging.

The final monitoring result was:

```text
Review decision: APPROVED
Checks: 4 passing
State: MERGED
Thread: 4 comments/reviews
```

The monitor's final close-out recommendation was:

```text
- state: merged
- move: Merged-PR close-out: a thank-you, plus a note on closing #278
  and the one item left out of scope
```

**Final outcome:** PR #286 was approved and merged into the upstream repository on November 14, 2026.

This experience demonstrated the value of monitoring the complete pull request lifecycle. PR-Monitor helped me distinguish a quiet period from a review requiring attention, avoid an unnecessary reminder, and record the final approval and merge.

## Inventory

The following tools support the workflow but are not featured individually.

- **Repro-check:** Evaluates an issue claim and reproduction report. I refined its rubric after discovering that environment details and the repository revision are important to making a reproduction independently verifiable. The original rubric was replaced with a shorter version, reducing its size from 8,381 bytes to 3,720 bytes. Its Unit 2 evaluation recorded 20/20.
- **Plan-check:** Evaluates the implementation plan against the issue and reproduction evidence. Its rubric contains eight required checks. The Unit 3 evaluation recorded 19/20, with one disagreement involving whether the diagnosis, target files, proposed approach, and evidence uncertainty were sufficiently established.
- **Voice-Guard:** Helps keep tool-generated writing consistent with my own communication style and prevents certain incomplete or unsupported GitHub actions. On November 1, 2026, I changed the individual skill voice guides to point to a shared voice guide at `~/.claude/voice-guard/voice-guide.md`. On November 8, I added a PreToolUse hook for GitHub issue, comment, and pull request commands.

## The Arc

**Beat 1**

Date: September 21, 2026

Friction: My initial issue-selection rubric was too strict about how specifically an issue described its implementation. This risked rejecting useful issues that had a clear goal but left implementation choices to the contributor.

Change: I refined the scope judgment so that it focused on whether the issue had an identifiable and feasible goal instead of requiring the issue author to prescribe the implementation.

Result: The issue-selection process became more useful for evaluating real open-source issues without treating every open design detail as a reason for rejection.

**Beat 2**

Date: September 24, 2026

Friction: The initial Repro-check rubric was lengthy and did not make the importance of environment and repository-revision evidence sufficiently clear.

Change: I replaced the original 8,381-byte rubric with a shorter 3,720-byte version and strengthened the requirements for environment details and repository revision information.

Result: The Unit 2 evaluation recorded 20/20. The updated rubric gave the reproduction process a more focused set of requirements.

**Beat 3**

Date: October 4, 2026

Friction: A plan can appear reasonable while leaving uncertainty about the diagnosis, files to change, implementation approach, or supporting evidence.

Change: I created Plan-check with eight required checks to evaluate the plan against the issue and reproduction evidence.

Result: The Unit 3 evaluation recorded 19/20. One disagreement remained around how strictly the tool should judge the diagnosis, target files, approach, and evidence uncertainty. This showed that a structured rubric can still have judgment boundaries that need refinement.

**Beat 4**

Date: November 7, 2026

Friction: Issue search results included candidates that looked suitable but had unclear contribution policies, language barriers, or implementation details that made the scope difficult to judge. Issues could also become unavailable while a search was in progress.

Change: I revised Issue-Scout to use a scope-feasibility check, require understandable issue language, look for evidence that outside contributions were welcome, separate difficulty from the accept/reject decision, and limit full evaluations to fifteen candidates.

Result: The search process examined 641 unique candidates and recorded five accepted issues in the broader scan. I selected `oscarbol09/jvm-mcp#278` for the TLS negotiation bug. A separate scan report also showed the fifteen-candidate evaluation limit and rejected candidates that failed the language or availability checks.

**Beat 5**

Date: November 8, 2026

Friction: The `jvm-mcp` contribution exposed several problems between finishing the implementation and opening a pull request. The Windows environment did not recognize the original Maven Wrapper command, and the pull request description did not initially satisfy the repository's template.

Change: I corrected the Maven Wrapper command in the Ship-Gate manifest, ran Spotless and the full Maven verification, and updated the pull request description to include the required template sections and testing evidence.

Result: Ship-Gate and PR-Precheck both passed. The workflow allowed PR #286 to be opened after the required checks and description corrections were complete.

**Beat 6**

Date: November 9–14, 2026

Friction: The first implementation used the generic TLS protocol name to allow protocol negotiation. Although the focused tests passed, the CI security scan flagged the generic SSL context configuration. Separately, the pull request needed maintainer feedback, and mergeability alone did not indicate approval.

Change: I revised the implementation to use the explicit TLS 1.3 protocol configuration, updated the related test expectations, reran the focused tests, and addressed the review feedback. I used PR-Monitor to distinguish waiting from sending a follow-up and to track the final review state.

Result: The focused tests passed, the maintainer confirmed TLS 1.3 negotiation and TLS 1.2 fallback on JDK 21+, the security scan was resolved, and the integration tests and CI checks passed. PR #286 was approved and merged on November 14, 2026.

## Limitations and What's Next

The toolkit makes my contribution workflow more consistent, but it does not replace human judgment or maintainer review.

Issue-Scout depends on the quality and freshness of public GitHub information. An issue can become unavailable after it has been accepted, and a repository's contribution policy may be difficult to interpret. Its evaluation limit also means that it may stop before every possible candidate has been examined.

PR-Precheck can identify missing evidence, incomplete descriptions, and repository-standard problems, but a passing result does not prove that an implementation is secure or technically correct. The `jvm-mcp` contribution demonstrated this limitation: the generic TLS configuration passed the package checks but was later flagged by the security scan.

Ship-Gate depends on a correctly configured manifest and commands that work in the actual development environment. A command that works in one shell or operating system may fail in another. Its checks are only as complete as the manifest, so repository-specific security checks must be included where appropriate.

PR-Monitor depends on receiving the correct pull request reference and being able to interpret the current GitHub state. A branch name alone did not always identify the pull request. Its timing recommendations are guidance, not a substitute for understanding maintainer communication or repository expectations. A mergeable pull request is not necessarily approved.

The tools can also become inconsistent if the installed skill versions and the copies stored in the coursework repository are changed separately. I found differences between the installed and repository copies of PR-Precheck's rubric, skill instructions, and procedure. Keeping these versions synchronized is an important maintenance task.

**Tool limits:**

issue-scout: Search results can become stale while candidates are being evaluated, and the fifteen-candidate full-evaluation limit can stop a scan before all potentially useful issues have been reviewed.

pr-precheck: It evaluates the contribution package and repository conventions, but it cannot independently prove that the code is secure or that every technical risk has been addressed.

ship-gate: It enforces only the checks configured in its manifest. Commands must match the operating system and shell, and missing checks can allow problems to pass through the gate.

pr-monitor: It needs a reliable pull request reference and current GitHub information. Its waiting and follow-up recommendations are based on the playbook and should not replace judgment about maintainer communication.

**What's next:**

- Keep the installed skill folders and coursework repository copies synchronized so that I know which rubric and procedure are being used.
- Add relevant security checks to Ship-Gate where the target repository provides them, rather than relying only on formatting and test verification.
- Continue refining rubric examples for borderline cases, especially around issue scope, contribution eligibility, and plan evidence.
- Improve PR-Monitor's handling of branch names and pull request identification so that it can recover the correct pull request more reliably.
- Keep recording actual tool outcomes, including failures and disagreements, so that future changes are based on observed problems rather than assumptions.

The most important lesson from building this toolkit is that each tool should support a specific decision, show the evidence behind that decision, and make its limitations clear. The tools helped me move from issue discovery through implementation, verification, pull request review, and merge, while also showing where automated checks still need human judgment.
