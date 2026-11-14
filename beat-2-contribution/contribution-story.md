

# Contribution: Enable TLS 1.3 Negotiation for Insecure Actuator Connections

**Contribution Number:** [1]  
**Student:** Sangeetha Eswaran  
**GitHub Username:** Sangeetha229  
**Issue:** [Issue #278](https://github.com/oscarbol09/jvm-mcp/issues/278)  
**Pull Request:** [PR #286](https://github.com/oscarbol09/jvm-mcp/pull/286)  
**Final Status:** Approved and merged on November 14, 2026

## Toolkit

**Unit 5: Issue-Scout — November 7, 2026**

I used Issue-Scout to search for and evaluate potential contribution issues based on my Java experience, interests, and contribution preferences. I reviewed candidate issues for repository activity, maintainer responsiveness, scope, existing work, and contribution fit. I selected issue #278 and posted a claim comment.

**Unit 6: Voice-Guard — November 8, 2026**

I used Voice-Guard to check outgoing GitHub communication against my centralized voice guide. I configured the Claude Code PreToolUse hook to inspect GitHub commands that create issues, comments, and pull requests. I also maintained rules for avoiding unsupported claims, generic comments, and unnecessary communication.

**Unit 7: Ship-Gate — November 8, 2026**

I used Ship-Gate to check the contribution before opening the pull request. My manifest included Spotless formatting validation and the full Maven verification. My first attempt exposed a Windows command-path issue because the manifest used `./mvnw`. I corrected the manifest to use the Windows Maven Wrapper command, `.\mvnw.cmd`, and reran the checks successfully. My direct development and testing commands were run in Git Bash using `./mvnw`. All required Ship-Gate checks passed.

**Unit 8: PR-Monitor — November 8–14, 2026**

I installed PR-Monitor, created a repository-specific playbook, and ran both its state check and full monitoring workflow. I used five recent merged pull requests to establish a three-calendar-day initial response threshold. The monitor drafted a wait note for PR #286 because the threshold had not been reached. I reviewed the draft and kept it off GitHub.

I continued monitoring the pull request as its review progressed. On November 14, the final state report confirmed that PR #286 was merged and approved, with four passing checks. The report also showed three comments/reviews and recorded the final activity. This completed the monitoring workflow from the initial waiting period through the merged outcome.

## Why I Chose This Issue

I searched for beginner-friendly Java issues using GitHub search commands and the Issue-Scout skill I developed in Claude Code. The skill uses my experience, preferences, search criteria, and evaluation rubric to identify issues that are relevant to my background and manageable in scope.

I also explored the [Open Source Issue Tracker](https://open-source-issue-tracker.onrender.com/) to find suitable contribution opportunities. However, I found that many beginner-friendly issues were stale. In a different repository, a claim comment I had posted was deleted. This did not happen to Issue #278; my claim comment for the current issue remained available. I also encountered situations where another contributor opened a pull request after I had started investigating an issue. Since I could not always complete an issue immediately due to limited time, these experiences taught me the importance of checking recent activity, verifying existing work, and selecting issues that I could realistically investigate and complete.

The latest Issue-Scout scan evaluated 641 unique candidates. Five issues were accepted after full evaluations, and issue #278 in `oscarbol09/jvm-mcp` was selected as a good match for my Java backend experience. The skill rated it as a straightforward, focused bug fix.

This issue matched my preferences because I prefer focused backend bug fixes with a clearly identifiable cause and a manageable implementation scope rather than large architectural changes or issues with unclear requirements. It involved Java source code, an understandable TLS configuration problem, and two related production classes with corresponding tests. It also allowed me to use my existing Java, Git, GitHub, and testing experience while learning more about SSL configuration and TLS protocol negotiation.

I chose issue #278 because it identifies a specific problem in two Java classes: `ActuatorClient` and `ActuatorProbe`. Both originally used a hardcoded TLS 1.2 protocol, which prevented successful connections to endpoints configured to support TLS 1.3 only. The expected outcome was to address the protocol compatibility problem while preserving the existing certificate-trust behavior.

On November 7, 2026, I posted a claim comment to let the maintainers know I intended to work on the issue. I then investigated the affected code, reproduced the problem, implemented a change, and prepared a pull request. This contribution helped me understand Java SSL configuration, HTTPS test fixtures, TLS protocol settings, and the importance of testing and reviewing changes before submission.

The Issue-Scout helped narrow down the search, but I still needed to verify the issue's behavior through reproduction and inspect the existing implementation before making changes.

Claim comment: [Issue #278 claim comment](https://github.com/oscarbol09/jvm-mcp/issues/278#issuecomment-6711994926)

**Toolkit**

- Tool: Issue-Scout.
- What it produced: A ranked set of candidate issues evaluated against my experience, interests, repository activity, issue scope, and existing work.
- Difficulties: Some candidates had unclear scope, insufficient maintainer activity, or existing contribution work.
- What changed: I refined the search and evaluation criteria and selected issue #278 because it had a focused Java backend problem that matched my skills.

## Understanding the Issue

**Problem Description**

The Actuator client and probe use HTTPS connections to communicate with Spring Boot Actuator endpoints. Their SSL contexts were originally initialized using the specific protocol name `TLSv1.2`. This restricted the configured protocol and caused connections to fail when an HTTPS endpoint supported TLS 1.3 only.

The issue requested support for TLS 1.3 when connecting to insecure Actuator HTTPS endpoints.

**Expected Behavior**

The Actuator client and probe should be able to connect to HTTPS Actuator endpoints that support TLS 1.3, while preserving the existing behavior for insecure certificate trust.

**Current Behavior**

The original implementation explicitly requested `TLSv1.2`. During baseline testing, the TLS 1.3-only HTTPS test endpoint could not complete the connection successfully.

The client test reported an `SSLHandshakeException`, and the probe test returned an empty `Optional` instead of the expected beans payload.

**Affected Components**

- `core/src/main/java/dev/jvmmcp/core/spring/ActuatorClient.java`
- `core/src/main/java/dev/jvmmcp/core/spring/ActuatorProbe.java`
- `core/src/test/java/dev/jvmmcp/core/spring/ActuatorClientTest.java`
- `core/src/test/java/dev/jvmmcp/core/spring/ActuatorProbeTest.java`

**Toolkit**

- Tool: Issue-Scout and manual source-code inspection.
- What it produced: An understanding of the reported TLS compatibility problem and the production classes and tests involved.
- Difficulties: The issue description identified the protocol compatibility problem, but I needed to reproduce it and inspect the implementation to understand the affected code.
- What changed: I traced the SSL context configuration in both production classes and identified the existing TLS 1.2-specific configuration as the implementation detail to address.

## Reproduction Process

**Environment Setup**

- Operating system: Windows 11.
- Java: Eclipse Temurin OpenJDK 21.0.13+9-LTS.
- Build tool: Maven Wrapper.
- Repository: `oscarbol09/jvm-mcp`.
- Implementation branch: `fix/278-actuator-tls-negotiation`.

The reproduction used a local JDK HTTPS server configured to support TLS 1.3 only. I used test code to exercise the affected Actuator client and probe against that endpoint.

**Steps to Reproduce**

1. Check out the original implementation before applying the fix.
2. Run the focused Actuator tests:

```bash
./mvnw -pl core "-Dtest=ActuatorProbeTest,ActuatorClientTest" test
```

3. Observe the behavior when the tests connect to the TLS 1.3-only endpoint.
4. Review the client and probe test results to identify the failed connection behavior.
5. Compare the results after changing the SSL context configuration.

**Reproduction Evidence**

The TLS 1.3-only reproduction cases were added as part of my test work to demonstrate the reported problem. The baseline result therefore included those reproduction tests; it was not a result from an untouched upstream test suite that already contained the same TLS 1.3-only test cases.

Before the initial implementation change, the focused test run reported:

- Total tests: 68.
- Failures: 1.
- Errors: 1.
- Skipped: 0.

The `ActuatorClientTest` TLS 1.3-only endpoint test failed with an `SSLHandshakeException` stating that the remote host terminated the handshake.

The `ActuatorProbeTest` TLS 1.3-only endpoint test returned an empty `Optional` instead of the expected beans payload.

After the initial implementation changed the SSL context from `TLSv1.2` to generic `TLS`, the focused test run reported 70 passing tests.

After the later revision to explicit `TLSv1.3`, I reran the focused tests. The revised implementation also passed all 70 tests:

- `ActuatorProbeTest`: 27 passed.
- `ActuatorClientTest`: 43 passed.
- Total tests: 70.
- Failures: 0.
- Errors: 0.

The final maintainer review independently confirmed that the TLS 1.2/1.3 matrix integration tests passed.

**Toolkit**

- Tool: Reproduction test code and Maven Wrapper.
- What it produced: A baseline reproduction showing the TLS 1.3-only endpoint failure and passing focused test results after the implementation changes.
- Difficulties: The baseline failure was visible through the client handshake exception and probe result, but the exact server-side handshake cause was not captured.
- What changed: I documented the observed failure without assuming an unverified low-level TLS error and used the reproduction tests to guide the implementation.

## Solution Approach

**Analysis**

Both `ActuatorClient.java` and `ActuatorProbe.java` originally initialized their SSL contexts with:

```java
SSLContext.getInstance("TLSv1.2");
```

This explicitly selected TLS 1.2 and did not address the issue of connecting to an endpoint that supported only TLS 1.3.

My initial implementation changed the protocol name to generic `TLS`:

```java
SSLContext.getInstance("TLS");
```

The focused tests passed with that implementation. However, the CI Security & Secret Scan reported blocking Semgrep findings against the generic TLS SSL context in both production classes.

This meant that passing local tests alone was not sufficient. I needed to respond to the security scan findings and revise the implementation.

**Revised Solution**

I updated both production classes to use:

```java
SSLContext.getInstance("TLSv1.3");
```

This replaced the generic `TLS` implementation with an explicit TLS 1.3 context, addressing the implementation change prompted by the Semgrep findings.

I preserved the existing insecure certificate-trust behavior and warnings because the issue concerned TLS protocol compatibility, not a redesign of certificate validation.

I also updated the related test expectation to reflect the revised SSL context configuration.

The maintainer later verified that this implementation correctly allows TLS 1.3 negotiation while maintaining fallback to TLS 1.2 on JDK 21+.

**Implementation Plan**

1. Reproduce the failure using the focused Actuator tests.
2. Inspect the SSL context initialization in both production classes.
3. Replace the TLS 1.2-specific SSL context configuration.
4. Add test coverage for the TLS 1.3-only endpoint and the relevant existing behavior.
5. Run the focused tests.
6. Run Spotless formatting validation.
7. Run the full Maven verification.
8. Review the final diff and ensure only the planned files are changed.
9. Respond to CI findings and revise the implementation as needed.
10. Push the updated branch to my fork and maintain the pull request against the upstream repository.

**Toolkit**

- Tool: Claude Code, source-code inspection, Maven Wrapper, and GitHub Actions.
- What it produced: A focused implementation plan and changes in two production classes and two test classes.
- Difficulties: The initial generic `TLS` implementation passed the focused tests but triggered blocking Semgrep findings in CI.
- What changed: I revised the SSL context configuration to explicitly use `TLSv1.3`, updated the corresponding test expectation, and reran local verification.

## Testing Strategy

The testing strategy focused on reproducing the original behavior and checking the implementation after the change.

The baseline focused test run included the TLS 1.3-only reproduction cases that I added. It reported 68 tests, with one failure and one error. The client test showed an SSL handshake failure for the TLS 1.3-only endpoint, while the probe test returned an empty result instead of the expected payload.

After the initial change to generic `TLS`, the focused test suite contained 70 tests, all passing. After the subsequent revision to explicit `TLSv1.3`, I reran the same focused tests and again obtained 70 passing tests: 27 in `ActuatorProbeTest` and 43 in `ActuatorClientTest`, with no failures or errors.

The CI Security & Secret Scan initially reported two blocking Semgrep findings against the generic `TLS` SSL context. I responded by changing both production classes to use `TLSv1.3`.

After this revision, I ran the full Maven verification locally. The reported results were:

- Core module: 595 tests passed.
- MCP server module: 469 tests passed.
- Four Docker-dependent tests were skipped because Docker was unavailable.
- Spotless formatting validation passed.
- JaCoCo checks passed.
- Full Maven verification completed with `BUILD SUCCESS`.
- `git diff --check` passed.

The final maintainer review confirmed that the revised implementation cleanly resolved the SAST check. The maintainer also confirmed that the TLS 1.2/1.3 matrix integration tests passed and that CI was fully green across all runners.

**Commands used in Git Bash:**

```bash
./mvnw -pl core "-Dtest=ActuatorProbeTest,ActuatorClientTest" test
./mvnw spotless:check
./mvnw clean verify
git diff --check
```

**Toolkit**

- Tool: Maven Wrapper, Spotless, JaCoCo, Ship-Gate, and GitHub Actions.
- What it produced: Focused test results for the revised implementation, local full-verification results, and final CI confirmation.
- Difficulties: The initial Ship-Gate manifest used a Unix-style Maven Wrapper command that did not work in the Windows environment. The initial generic TLS implementation also triggered security scan findings.
- What changed: I updated the manifest to use `.\mvnw.cmd`, reran the checks, and revised the SSL context configuration in response to the Semgrep findings.

## Implementation Notes

**Initial Implementation — November 8, 2026**

I first changed the SSL context initialization in the two production classes:

- `ActuatorClient.java`
- `ActuatorProbe.java`

The initial change replaced `SSLContext.getInstance("TLSv1.2")` with `SSLContext.getInstance("TLS")`.

I updated the corresponding test classes:

- `ActuatorClientTest.java`
- `ActuatorProbeTest.java`

The tests included TLS 1.2 and TLS 1.3 endpoint coverage, and I generalized the HTTPS test helper to accept a protocol parameter.

The initial implementation preserved the existing insecure certificate-trust behavior and its warnings. I committed this version with:

```text
0570361fbf93cb648fbf0323df3f15283f934ec7
```

Commit message:

```text
fix(spring): allow TLS protocol negotiation for insecure actuator connections
```

**Revision Following CI Feedback — November 2026**

The CI Security & Secret Scan reported two blocking Semgrep findings against the generic `TLS` SSL context in the production classes.

I revised both classes to use:

```java
SSLContext.getInstance("TLSv1.3");
```

I also updated the corresponding test expectation in `ActuatorProbeTest`.

I committed and pushed this revision with:

```text
ff484ee27340bb13b7185c3ee218a65f4435efa8
```

Commit message:

```text
fix(spring): use explicit TLS 1.3 context for insecure actuator connections
```

I verified that the pull request head pointed to this commit. The revised implementation passed the focused 70-test suite and the local full Maven verification.

**Pull Request and Monitoring**

I opened PR #286 against the upstream `main` branch using my fork branch `fix/278-actuator-tls-negotiation`.

I installed PR-Monitor in `~/.claude/pr-monitor/` and created a playbook with response, staleness, nudge, and walk-away rules. To establish a practical waiting threshold, I examined five recent merged pull requests from distinct external contributors and compared the time to their first maintainer response. Based on that small sample, I set a three-calendar-day threshold and documented that it was a guideline rather than a guaranteed response time.

I ran the PR-Monitor state check and confirmed that it detected PR #286 in the upstream repository. The report distinguished GitHub's mergeability status from its review and merge status. The PR being reported as `MERGEABLE` did not mean it was approved or ready to merge.

The monitor generated a wait note when the response threshold had not been reached. I reviewed the draft and did not post it. PR-Monitor did not post anything to GitHub.

On November 14, 2026, the maintainer confirmed the revised implementation and approved and merged the pull request. The maintainer's comment stated:

> Verified the update to `TLSv1.3`. It correctly allows negotiation of TLS 1.3 while maintaining fallback to TLS 1.2 on JDK 21+, cleanly resolves the SAST check, and passes the TLS 1.2/1.3 matrix integration tests. CI is fully green across all runners. Approving and merging.

The final PR-Monitor state report recorded:

```text
- checks: 4 passing
- review decision: APPROVED
- thread: 3 comment(s)/review(s)
- state: MERGED: the PR is merged
```

This confirmed that the contribution had progressed from an open pull request awaiting review to an approved and merged contribution.

**Toolkit**

- Tool: Git, Maven Wrapper, Claude Code, Ship-Gate, GitHub CLI, GitHub Actions, and PR-Monitor.
- What it produced: Four changed Java files, an initial implementation commit, a revised implementation commit, local verification results, a pushed feature branch, PR #286, a completed PR-Monitor playbook, and final monitoring evidence.
- Difficulties: I needed to keep the implementation within the issue's scope, ensure the Windows build commands worked in Ship-Gate, respond to the Semgrep findings, and distinguish a mergeable PR from an approved PR.
- What changed: I revised the implementation after CI feedback, pushed the new commit, and used PR-Monitor to track the review through approval and merge.

## Pull Request

**PR Link**

[PR #286 — Allow TLS protocol negotiation for insecure Actuator connections](https://github.com/oscarbol09/jvm-mcp/pull/286)

**PR Description**

The pull request is titled:

`Allow TLS protocol negotiation for insecure Actuator connections`

It targets `main` in the upstream repository `oscarbol09/jvm-mcp` and uses my fork branch:

`Sangeetha229:fix/278-actuator-tls-negotiation`

The initial implementation changed the SSL context from `TLSv1.2` to generic `TLS`. After the CI security scan reported blocking Semgrep findings, I revised both production classes to use `TLSv1.3`. The final implementation is recorded in commit `ff484ee`.

**Maintainer Feedback and CI**

The maintainer reviewed the revised implementation and confirmed:

- TLS 1.3 negotiation works.
- TLS 1.2 fallback is maintained on JDK 21+.
- The SAST check is clean.
- The TLS 1.2/1.3 matrix integration tests pass.
- CI is fully green across all runners.

The maintainer explicitly approved and merged the pull request on November 14, 2026.

**Final Status**

<badge color="success">Approved and merged</badge>

PR #286 is merged into the upstream repository. The final PR-Monitor report confirmed the merged state, an `APPROVED` review decision, and four passing checks.

**Toolkit**

- Tool: GitHub CLI (`gh`), Git, GitHub Actions, and Ship-Gate.
- What it produced: Pull request #286, a revision addressing the initial security scan findings, and a successfully merged upstream contribution.
- Difficulties: The PR description and implementation needed to remain consistent as the code changed in response to CI feedback.
- What changed: I revised the implementation, pushed the new commit, and completed the review process with maintainer approval and a successful merge.

## Learnings and Reflections

**Technical Skills Gained**

I gained a better understanding of Java SSL context configuration and TLS protocol settings. I learned how explicitly selecting a TLS protocol can affect compatibility with HTTPS endpoints and how to test different TLS configurations using local HTTPS test fixtures.

I also improved my experience with Maven multi-module builds, Java unit testing, Git branch management, GitHub CLI, and preparing a pull request that follows repository contribution guidelines.

I gained practical experience using Claude Code hooks and supporting tools to review outgoing communication, validate changes before submission, and monitor a pull request without automatically posting responses.

**Challenges Overcome**

One challenge was reproducing the TLS 1.3 failure consistently before changing the implementation. I used test code and its output to identify the affected client and probe behavior.

Another challenge was responding to the Semgrep findings. The initial generic `TLS` implementation passed the focused tests but was rejected by the security scan. I had to revise the implementation rather than treating passing tests as sufficient evidence.

I also encountered a Windows command-path issue in the Ship-Gate manifest. I corrected the Maven Wrapper command and reran the checks successfully.

During Unit 8, I needed to distinguish between GitHub reporting that a PR was mergeable and the PR actually being approved or eligible to merge. The monitor's report showed `MERGEABLE`, but the merge state was `BLOCKED` and the review decision was `CHANGES_REQUESTED`. This helped me interpret the report accurately instead of treating mergeability as approval.

The final review demonstrated the importance of responding to feedback and validating changes at multiple levels. The maintainer confirmed that the TLS 1.3 update preserved TLS 1.2 fallback on JDK 21+, resolved the SAST finding, passed the TLS protocol integration matrix, and achieved green CI across all runners.

I also learned to distinguish between what a test observes and what I infer from it. A successful connection to a TLS 1.3-only endpoint is useful evidence, but without an explicit assertion on the negotiated session protocol, I should not claim that my local test independently verified the negotiated protocol. The maintainer's final integration-test review provided additional confirmation of the protocol behavior.

**What I'd Do Differently Next Time**

I would inspect the repository's SSL configuration and test fixtures earlier, then identify the smallest reproducible test case before starting implementation. I would also review the repository's security scanning rules earlier so that I could consider CI requirements alongside local test results.

For future contributions, I would explicitly assert the negotiated session protocol when that behavior is important to the issue. I would also keep a clear record of which test results belong to each implementation revision.

Finally, I would preview rendered Markdown before committing documentation. Broken code fences can hide commands and make an otherwise useful contribution story difficult to review.

## Resources Used

- Issue #278: https://github.com/oscarbol09/jvm-mcp/issues/278
- Pull request #286: https://github.com/oscarbol09/jvm-mcp/pull/286
- Repository: https://github.com/oscarbol09/jvm-mcp
- Repository contribution guidelines: `CONTRIBUTING.md`
- Repository pull request template: `.github/PULL_REQUEST_TEMPLATE.md`
- Java `SSLContext` API documentation: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/javax/net/ssl/SSLContext.html
- Maven Wrapper documentation: https://maven.apache.org/wrapper/
- GitHub CLI documentation: https://cli.github.com/manual/
- Unit 8 PR-Monitor materials: `skill/INSTALL.md`, `skill/monitor.py`, and `skill/playbook-template.md` from the AI301 Unit 8 starter repository.
- Personal PR-Monitor playbook: `~/.claude/pr-monitor/playbook.md`
- PR-Monitor run report: `~/.claude/pr-monitor/run-report.md`
- PR-Monitor log: `~/.claude/pr-monitor/monitor-log.md`

