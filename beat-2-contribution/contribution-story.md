# Contribution: Enable TLS 1.3 Negotiation for Insecure Actuator Connections

Path: `beat-2-contribution/contribution-story.md`

**Contribution Number:** [1]  
**Student:** Sangeetha Eswaran  
**GitHub Username:** Sangeetha229  
**Issue:** [https://github.com/oscarbol09/jvm-mcp/issues/278](https://github.com/oscarbol09/jvm-mcp/issues/278)  
**Pull Request:** [https://github.com/oscarbol09/jvm-mcp/pull/286](https://github.com/oscarbol09/jvm-mcp/pull/286)

## Toolkit

**Unit 5: Issue-Scout — November 7, 2026**

I used issue-scout to search for and evaluate potential contribution issues based on my Java experience, interests, and contribution preferences. I reviewed candidate issues for repository activity, maintainer responsiveness, scope, existing work, and contribution fit. I selected issue #278 and posted a claim comment.

**Unit 6: Voice-Guard — November 8, 2026**

I used voice-guard to check outgoing GitHub communication against my centralized voice guide. I configured the Claude Code PreToolUse hook to inspect GitHub commands that create issues, comments, and pull requests. I also maintained rules for avoiding unsupported claims, generic comments, and unnecessary communication.

**Unit 7: Ship-Gate — November 8, 2026**

I used Ship-Gate to check the contribution before opening the pull request. My manifest included Spotless formatting validation and the full Maven verification. My first attempt exposed a Windows command-path issue because the manifest used `./mvnw`; I corrected it to `.\mvnw.cmd` and reran the checks. All required checks passed.

**Unit 8: PR-Monitor — November 8, 2026**

I installed PR-Monitor, created a repository-specific playbook, and ran both its state check and full monitoring workflow. I used five recent merged pull requests to establish a three-calendar-day initial response threshold. The monitor drafted a wait note for PR #286 because the threshold had not been reached; I kept the draft off GitHub and clarified that I should rerun the monitor on November 12 only if the discussion is still quiet.

## Why I Chose This Issue

I searched for beginner-friendly Java issues using GitHub search commands and the Issue-Scout skill I developed in Claude Code. The skill uses my experience, preferences, search criteria, and evaluation rubric to identify issues that are relevant to my background and manageable in scope.

I also explored the [Open Source Issue Tracker](https://open-source-issue-tracker.onrender.com/) to find suitable contribution opportunities. However, I found that many beginner-friendly issues were stale. Some of the issues I claimed had their claim comments deleted, and in a few cases another contributor opened a pull request after I had started working on the issue. Since I could not always complete an issue immediately due to limited time, these experiences taught me the importance of checking recent activity, verifying existing work, and selecting issues that I can realistically investigate and complete.

The latest Issue-Scout scan evaluated 641 unique candidates. Five issues were accepted after full evaluations, and issue #278 in `oscarbol09/jvm-mcp` was selected as a good match for my Java backend experience. The skill rated it as a straightforward, focused bug fix.

This issue matched my preferences because I prefer focused backend bug fixes with a clearly identifiable cause and a manageable implementation scope rather than large architectural changes or issues with unclear requirements. It involved Java source code, an understandable TLS configuration problem, and two related production classes with corresponding tests. It also allowed me to use my existing Java, Git, GitHub, and testing experience while learning more about SSL configuration and TLS protocol negotiation.

I chose issue #278 because it identifies a specific problem in two Java classes: `ActuatorClient` and `ActuatorProbe`. Both use a hardcoded TLS 1.2 protocol, which can prevent connections to servers that support only TLS 1.3. The expected outcome was clear: support TLS protocol negotiation while preserving the existing certificate-trust behavior. This provided an opportunity to reproduce a specific failure, implement a targeted fix, and verify the result with automated tests.

On November 7, 2026, I posted a claim comment to let the maintainers know I intended to work on the issue. On November 8, I completed the reproduction, investigation, implementation, testing, and pull request preparation. This contribution helped me understand Java SSL configuration, protocol negotiation, HTTPS test fixtures, and the importance of testing both existing and newer TLS protocol behavior.

The Issue-Scout helped narrow down the search, but I still needed to verify the issue's behavior through reproduction and inspect the existing implementation before making changes.


Claim comment: [https://github.com/oscarbol09/jvm-mcp/issues/278#issuecomment-6711994926](https://github.com/oscarbol09/jvm-mcp/issues/278#issuecomment-6711994926)

**Toolkit**

- Tool: issue-scout.
- What it produced: A ranked set of candidate issues evaluated against my experience, interests, repository activity, issue scope, and existing work.
- Difficulties: Some candidates had unclear scope, insufficient maintainer activity, or existing contribution work.
- What changed: I refined the search and evaluation criteria and selected issue #278 because it had a focused Java backend problem that matched my skills.

## Understanding the Issue

**Problem Description**

The Actuator client and probe use HTTPS connections to communicate with Spring Boot Actuator endpoints. Their SSL context was initialized using the specific protocol name `TLSv1.2`. This restricted protocol negotiation and caused connections to fail when an HTTPS endpoint supported TLS 1.3 only.

The issue requested support for TLS 1.3 when connecting to insecure Actuator HTTPS endpoints.

**Expected Behavior**

The Actuator client and probe should support TLS protocol negotiation so that connections can work with TLS 1.2 and TLS 1.3 endpoints, provided the Java runtime and server support those protocols.

**Current Behavior**

The original implementation explicitly requested `TLSv1.2`. During baseline testing, the TLS 1.3-only HTTPS test endpoint could not complete the connection successfully.

The client test reported an `SSLHandshakeException`, and the probe test returned an empty `Optional` instead of the expected beans payload.

**Affected Components**

- `core/src/main/java/dev/jvmmcp/core/spring/ActuatorClient.java`
- `core/src/main/java/dev/jvmmcp/core/spring/ActuatorProbe.java`
- `core/src/test/java/dev/jvmmcp/core/spring/ActuatorClientTest.java`
- `core/src/test/java/dev/jvmmcp/core/spring/ActuatorProbeTest.java`

**Toolkit**

- Tool: issue-scout and manual source-code inspection.
- What it produced: An understanding of the reported TLS negotiation problem and the production classes and tests involved.
- Difficulties: The issue description identified the protocol compatibility problem, but I needed to reproduce it and inspect the implementation to understand the affected code.
- What changed: I traced the SSL context configuration in both production classes and identified the existing TLS 1.2-specific configuration as the implementation detail to address.

## Reproduction Process

**Environment Setup**

- Operating system: Windows.
- Java: Eclipse Temurin OpenJDK 21.0.13+9 LTS.
- Build tool: Maven Wrapper.
- Repository: `oscarbol09/jvm-mcp`.
- Branch for implementation: `fix/278-actuator-tls-negotiation`.

The reproduction used the project's existing HTTPS test fixtures and a local test endpoint configured to support TLS 1.3 only.

**Steps to Reproduce**

1. Check out the original implementation before applying the fix.
2. Run the focused Actuator tests:

   ```powershell
   .\mvnw.cmd -pl core "-Dtest=ActuatorProbeTest,ActuatorClientTest" test
   ```

3. Observe the TLS 1.3-only endpoint behavior.
4. Review the client and probe test results to identify the failed connection behavior.
5. Compare the behavior after changing the SSL context configuration and adding TLS 1.3 coverage.

**Reproduction Evidence**

Before the fix, the focused test run reported:

- Total tests: 68.
- Failures: 1.
- Errors: 1.
- Skipped: 0.

The `ActuatorClientTest` TLS 1.3-only endpoint test failed with an `SSLHandshakeException` stating that the remote host terminated the handshake.

The `ActuatorProbeTest` TLS 1.3-only endpoint test returned an empty `Optional` instead of the expected beans payload.

The baseline full verification also reported the same TLS-related failure and error.

After the fix, the focused test run reported:

- `ActuatorProbeTest`: 27 passed.
- `ActuatorClientTest`: 43 passed.
- Total tests: 70.
- Failures: 0.
- Errors: 0.

**Toolkit**

- Tool: repro-check and Maven Wrapper.
- What it produced: A baseline reproduction report showing the TLS 1.3-only endpoint failure before the fix and successful focused tests after the fix.
- Difficulties: The baseline failure was visible through the client handshake exception and probe result, but the exact server-side handshake cause was not captured.
- What changed: I documented the observed failure without assuming an unverified low-level TLS error and used the same focused tests to verify the fix.

## Solution Approach

**Analysis**

Both `ActuatorClient.java` and `ActuatorProbe.java` initialized their SSL contexts with:

```java
SSLContext.getInstance("TLSv1.2");
```

Using the protocol-specific name restricted the SSL context configuration to TLS 1.2. The implementation needed to allow the Java runtime to negotiate a supported TLS protocol rather than explicitly selecting TLS 1.2.

The related tests also needed to demonstrate that both TLS 1.2 and TLS 1.3 endpoints continued to work.

**Proposed Solution**

Change the SSL context initialization in both production classes from:

```java
SSLContext.getInstance("TLSv1.2");
```

to:

```java
SSLContext.getInstance("TLS");
```

This allows the Java runtime to negotiate a supported TLS protocol with the HTTPS endpoint.

Preserve the existing insecure certificate-trust behavior because the issue concerns protocol negotiation, not a change to the certificate validation design.

Add or update tests to verify TLS 1.2 and TLS 1.3 behavior for both the Actuator client and probe.

**Implementation Plan**

1. Reproduce the failure using the existing focused Actuator tests.
2. Inspect the SSL context initialization in both production classes.
3. Replace the TLS 1.2-specific SSL context protocol with the generic `TLS` protocol.
4. Add test coverage for TLS 1.2 and TLS 1.3 endpoints.
5. Run the focused tests.
6. Run Spotless formatting validation.
7. Run the full Maven verification.
8. Review the final diff and ensure only the planned files are changed.
9. Push the branch to my fork and open a pull request against the upstream repository.

**Toolkit**

- Tool: Claude Code, source-code inspection, and manual implementation.
- What it produced: A focused implementation plan and changes in two production classes and two test classes.
- Difficulties: The test fixture needed to support testing different TLS protocol versions without duplicating unnecessary setup.
- What changed: I generalized the HTTPS test helper to accept a protocol parameter so the tests could exercise TLS 1.2 and TLS 1.3.

## Testing Strategy

The testing strategy focused on reproducing the original behavior and verifying that both TLS protocol versions worked after the change.

Before the fix, I ran the focused tests against the original implementation. The baseline result was 68 tests with one failure and one error. The client test showed an SSL handshake failure for the TLS 1.3-only endpoint, while the probe test returned an empty result instead of the expected payload.

After the fix, I ran the focused tests again. The client and probe tests passed, including the TLS 1.2 and TLS 1.3 endpoint coverage.

I also ran formatting validation and the complete Maven verification.

**Commands used:**

```powershell
.\mvnw.cmd -pl core "-Dtest=ActuatorProbeTest,ActuatorClientTest" test
```

```powershell
.\mvnw.cmd spotless:check
```

```powershell
.\mvnw.cmd clean verify
```

**Results after the fix:**

- Focused Actuator tests: 70 passed, 0 failures, 0 errors.
- Core module: 595 tests passed.
- MCP server module: 469 tests passed.
- Four Docker-dependent tests were skipped because Docker was unavailable.
- Spotless formatting validation passed.
- JaCoCo checks passed.
- Full Maven verification completed with `BUILD SUCCESS`.
- `git diff --check` passed.

**Toolkit**

- Tool: Maven Wrapper, Spotless, JaCoCo, and Ship-Gate.
- What it produced: Passing focused tests, formatting validation, and full Maven verification.
- Difficulties: The initial Ship-Gate manifest used a Unix-style Maven Wrapper command that did not work in the Windows environment.
- What changed: I updated the manifest to use `.\mvnw.cmd` and reran the checks. The required Ship-Gate checks passed.

## Implementation Notes

**November 8, 2026**

I implemented the TLS negotiation change in the two production classes:

- `ActuatorClient.java`
- `ActuatorProbe.java`

Both now use `SSLContext.getInstance("TLS")` instead of explicitly requesting `TLSv1.2`.

I updated the corresponding test classes:

- `ActuatorClientTest.java`
- `ActuatorProbeTest.java`

The tests cover TLS 1.2 and TLS 1.3 endpoints. I also generalized the HTTPS test helper to accept a protocol parameter.

The implementation preserved the existing insecure certificate-trust behavior and its warnings. The change remained limited to the two production classes and their two corresponding test classes.

I committed the implementation with:

```text
0570361fbf93cb648fbf0323df3f15283f934ec7
```

Commit message:

```text
fix(spring): allow TLS protocol negotiation for insecure actuator connections
```

I pushed the branch to my fork and verified that the remote branch pointed to the same commit as my local branch.

**November 8, 2026 — Pull Request and Monitoring**

I opened PR #286 against the upstream `main` branch using my fork branch `fix/278-actuator-tls-negotiation`.

I installed PR-Monitor in `~/.claude/pr-monitor/` and created a playbook with the required response, staleness, nudge, and walk-away rules. To establish a practical waiting threshold, I examined five recent merged pull requests from distinct external contributors and compared the time to their first maintainer response. Based on that small sample, I set a three-calendar-day threshold and documented that it was a guideline rather than a guaranteed response time.

I ran the PR-Monitor state check and confirmed that it detected PR #286 in the upstream repository. The report showed no checks reported, no review decision, zero comments or reviews, and a quiet thread. GitHub reported the PR as mergeable, but its merge state was blocked.

The full monitor run generated a wait note because the three-day threshold had not been reached. I reviewed the draft and did not post it. It also reminded me to verify the Windows Maven Wrapper command in the PR description. I clarified my next action: wait until November 12 and rerun the monitor only if the thread remains quiet. PR-Monitor did not post anything to GitHub.

**Toolkit**

- Tool: Git, Maven Wrapper, Claude Code, Ship-Gate, GitHub CLI, and PR-Monitor.
- What it produced: Four changed Java files, commit `0570361`, successful verification, a pushed feature branch, PR #286, a completed PR-Monitor playbook, a state report, and a drafted wait note.
- Difficulties: I needed to keep the implementation within the issue's scope, ensure the Windows-specific build commands worked in Ship-Gate, and distinguish a mergeable PR from an approved PR.
- What changed: I kept the changes limited to the affected production classes and tests, corrected the manifest command, verified the branch before opening the PR, and established a three-day monitoring threshold. I also kept the monitor's wait note off the public PR thread.

## Pull Request

**PR Link**

[https://github.com/oscarbol09/jvm-mcp/pull/286](https://github.com/oscarbol09/jvm-mcp/pull/286)

**PR Description**

The pull request is titled:

`Allow TLS protocol negotiation for insecure Actuator connections`

It targets `main` in the upstream repository `oscarbol09/jvm-mcp` and uses my fork branch:

`Sangeetha229:fix/278-actuator-tls-negotiation`

The PR explains the TLS negotiation problem, the change from `TLSv1.2` to `TLS`, the related test coverage, and the verification results. It also includes the issue reference `Fixes #278` and the AI assistance disclosure.

**Maintainer Feedback**

- November 8, 2026: none yet.

**Status**

Awaiting review (waiting on the maintainer).

Opened November 8, 2026.

**Toolkit**

- Tool: GitHub CLI (`gh`) and Ship-Gate.
- What it produced: Pull request #286 opened against the upstream `main` branch.
- Difficulties: The PR description needed to follow the repository's pull request template and include the required testing and verification information.
- What changed: I updated the draft to match the repository template, ran Ship-Gate successfully, pushed the branch, and opened the PR.

## Learnings and Reflections

**Technical Skills Gained**

I gained a better understanding of Java SSL context configuration and TLS protocol negotiation. I learned how explicitly selecting a TLS protocol can affect compatibility with HTTPS endpoints and how to test multiple TLS versions using local HTTPS test fixtures.

I also improved my experience with Maven multi-module builds, Java unit testing, Git branch management, GitHub CLI, and preparing a pull request that follows repository contribution guidelines.

I gained practical experience using Claude Code hooks and supporting tools to review outgoing communication, validate changes before submission, and monitor a pull request without automatically posting responses.

**Challenges Overcome**

One challenge was reproducing the TLS 1.3 failure consistently before changing the implementation. I used the existing tests and their output to identify the affected client and probe behavior.

Another challenge was making sure the updated tests covered both TLS 1.2 and TLS 1.3 without unnecessarily duplicating test setup. I generalized the HTTPS test helper to support different protocols.

I also encountered a Windows command-path issue in the Ship-Gate manifest. I corrected the Maven Wrapper command and reran the checks successfully.

During Unit 8, I needed to distinguish between GitHub reporting that a PR was mergeable and the PR actually being approved or eligible to merge. The monitor's report showed `MERGEABLE`, but the merge state was `BLOCKED` and there was no review decision. This helped me interpret the report accurately instead of treating mergeability as approval.

**What I'd Do Differently Next Time**

I would inspect the repository's SSL configuration and test fixtures earlier, then identify the smallest reproducible test case before starting implementation. I would also verify the exact commands in the target operating system before adding them to automated validation manifests.

For future contributions, I would establish review-wait expectations from repository history before opening the PR and document the reasoning in my playbook. I would use PR-Monitor when new activity arrives, review each draft against the actual PR discussion, and post only a response that is necessary and accurate.

## Resources Used

- Issue #278: [https://github.com/oscarbol09/jvm-mcp/issues/278](https://github.com/oscarbol09/jvm-mcp/issues/278)
- Pull request #286: [https://github.com/oscarbol09/jvm-mcp/pull/286](https://github.com/oscarbol09/jvm-mcp/pull/286)
- Repository: [https://github.com/oscarbol09/jvm-mcp](https://github.com/oscarbol09/jvm-mcp)
- Repository contribution guidelines: `CONTRIBUTING.md`
- Repository pull request template: `.github/PULL_REQUEST_TEMPLATE.md`
- Java `SSLContext` API documentation: [https://docs.oracle.com/en/java/javase/21/docs/api/java.base/javax/net/ssl/SSLContext.html](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/javax/net/ssl/SSLContext.html)
- Maven Wrapper documentation: [https://maven.apache.org/wrapper/](https://maven.apache.org/wrapper/)
- GitHub CLI documentation: [https://cli.github.com/manual/](https://cli.github.com/manual/)
- Unit 8 PR-Monitor materials: `skill/INSTALL.md`, `skill/monitor.py`, and `skill/playbook-template.md` from the AI301 Unit 8 starter repository.
- Personal PR-Monitor playbook: `~/.claude/pr-monitor/playbook.md`
- PR-Monitor run report: `~/.claude/pr-monitor/run-report.md`
- PR-Monitor drafted move: `~/.claude/pr-monitor/drafted-move.md`
