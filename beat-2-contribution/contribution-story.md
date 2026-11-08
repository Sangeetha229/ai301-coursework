# Contribution Story: Enable TLS 1.3 Negotiation for Insecure Actuator Connections

**Repository:** [oscarbol09/jvm-mcp](https://github.com/oscarbol09/jvm-mcp)  
**Issue:** [#278 – TLS 1.3 support for insecure Actuator HTTPS connections](https://github.com/oscarbol09/jvm-mcp/issues/278)  
**Pull Request:** Pending  
**Contribution Number:** 1

## Toolkit

### Unit 5 – Issue-Scout

I developed and improved an `issue-scout` skill in Claude Code to search for and evaluate beginner-friendly open-source issues. I refined its search strategy, candidate-selection rubric, and evaluation criteria through repeated testing. The skill considers my Java experience, contribution preferences, issue scope, repository activity, maintainer activity, and existing work.

The latest evaluation achieved **20/20 on the scored evaluation items**. The latest scan evaluated **641 unique candidates**, with five issues accepted from full evaluations.

### Unit 6 – Voice-Guard and Repository Memory

I developed a voice guard using Claude Code hooks and `check_voice.py` to check outgoing GitHub comments and prevent messages that violate my communication guidelines.

I also created a local `CLAUDE.local.md` file to preserve repository-specific contribution rules, build commands, and issue-related findings across Claude Code sessions. I centralized my voice guidance and configured a `PreToolUse` hook so communication rules can be checked before supported GitHub actions.

### Unit 7 – Plan-Check and Ship-Gate

I used the plan-check workflow to evaluate the proposed solution before implementation. The first evaluation identified missing TLS 1.2 control tests and an outdated Mockito expectation. I updated the plan to address both findings.

The final plan-check verdict was **ACCEPT**, with all eight checks passing: diagnosis, scope, file, approach, test plan, risks, evidence uncertainty, and comment conventions.

I installed and configured the ship-gate hook in Claude Code. I created a local `ship-manifest.md` containing the repository's formatting and full verification commands:

- `.\mvnw.cmd spotless:check`
- `.\mvnw.cmd clean verify`

I verified the manifest checks before and after implementation. The final formatting and full verification checks passed.

I also tested the ship-gate behavior with the repository's PR command help invocation. The hook stopped the command because there was no PR text to check. This confirmed that the ship-gate was enforcing its intended check rather than allowing an unchecked outgoing PR command.

### Unit 8 – PR-Monitor

**Status:** Pending. The implementation has been committed locally, but the pull request has not yet been created. I will document PR-Monitor setup and use after creating the pull request.

## Why I Chose This Issue

I searched for beginner-friendly Java issues using GitHub search commands and the Issue-Scout skill I developed in Claude Code. The skill uses my experience, preferences, search criteria, and evaluation rubric to identify issues that are relevant to my background and manageable in scope.

I also explored the [Open Source Issue Tracker](https://open-source-issue-tracker.onrender.com/) to find suitable contribution opportunities. However, I found that many beginner-friendly issues were stale. Some of the issues I claimed had their claim comments deleted, and in a few cases another contributor opened a pull request after I had started working on the issue. Since I could not always complete an issue immediately due to limited time, these experiences taught me the importance of checking recent activity, verifying existing work, and selecting issues that I can realistically investigate and complete.

The latest Issue-Scout scan evaluated **641 unique candidates**. Five issues were accepted from full evaluations, and this issue was selected as a good match for my Java backend experience. The skill rated it as a straightforward, focused bug fix.

I chose issue #278 because it identifies a specific problem in two Java classes: `ActuatorClient` and `ActuatorProbe`. Both use a hardcoded TLS 1.2 protocol, which can prevent connections to servers that require TLS 1.3. The proposed change was limited and provided an opportunity to learn more about Java SSL configuration, TLS protocol negotiation, and testing secure connections.

The Issue-Scout helped narrow down the search, but I still needed to verify the issue's behavior through reproduction and inspect the existing implementation before making changes.

**Claim comment:** Posted on November 7, 2026. The comment identifies the planned TLS 1.3-only reproduction and asks whether another contributor is already working on the issue.

**Toolkit:**
- Used GitHub search commands to discover Java issues.
- Used the Issue-Scout skill to filter and evaluate candidates based on my experience and preferences.
- Used the Open Source Issue Tracker to explore additional contribution opportunities.
- Reviewed issue activity and existing work before selecting the issue.

## Understanding the Issue

### Problem Description

Issue #278 reports that insecure HTTPS connections to Spring Boot Actuator endpoints may not work when the server requires TLS 1.3.

The repository has two separate classes that create custom SSL contexts for insecure HTTPS connections:

- `core/src/main/java/dev/jvmmcp/core/spring/ActuatorClient.java`
- `core/src/main/java/dev/jvmmcp/core/spring/ActuatorProbe.java`

Both classes originally used:

```java
SSLContext.getInstance("TLSv1.2");
```

This explicitly requests TLS 1.2 rather than allowing the Java runtime to negotiate a supported TLS version. As a result, a server that only permits TLS 1.3 may not be reachable through these insecure HTTPS paths.

### Expected Behavior

When insecure HTTPS connections are enabled, the Actuator client and probe should be able to negotiate a compatible TLS protocol, including TLS 1.3 when supported by both the Java runtime and the server.

The existing insecure-connection behavior, including its trust-all certificate handling and security warnings, should remain intact.

### Current Behavior

Source inspection identified that both `ActuatorClient` and `ActuatorProbe` explicitly requested `TLSv1.2` when creating their custom insecure SSL contexts.

The baseline reproduction against a TLS 1.3-only local HTTPS server confirmed that:

- The `ActuatorClient` regression test throws an `SSLHandshakeException`.
- The `ActuatorProbe` regression test receives an empty `Optional` instead of the expected beans payload. Its underlying exception was not captured.

The client error message differs from the wording reported in the original issue. The reproduction details and limitations are documented below.

### Affected Components

1. `ActuatorClient.java`
   - Creates the HTTP client used to retrieve Actuator health, metrics, and startup information.
   - Its insecure HTTPS path creates a custom SSL context.

2. `ActuatorProbe.java`
   - Discovers Actuator endpoints by checking candidate ports and requesting `/actuator/beans`.
   - Its insecure HTTPS path also creates a custom SSL context.

3. `ActuatorProbeTest.java`
   - Contains HTTPS discovery tests and a test that mocks `SSLContext.getInstance(...)`.
   - The mock expectation needed to match the updated protocol selection.

4. `ActuatorClientTest.java`
   - Contains tests for insecure client construction.
   - TLS 1.3-specific HTTPS regression coverage was added.

**Toolkit:**
- Used Claude Code to inspect the repository and trace the SSL context creation paths.
- Reviewed the relevant production classes and existing tests.
- Used the repository's `CONTRIBUTING.md` to understand project conventions and testing requirements.

## Reproduction Process

### Environment Setup

The baseline reproduction was performed on the original production implementation at revision `a54bee4e0179bd8a0de61e843c1b7e65df4bb46a`. The regression tests were added locally to the two test classes; production code remained unchanged.

| Component | Details |
|---|---|
| Operating system | Windows 11 Home, Version 10.0.26200, x64 |
| Java distribution | Eclipse Temurin OpenJDK |
| Java version | 21.0.13 LTS |
| Java runtime build | 21.0.13+9-LTS |
| JVM | 64-Bit Server VM |
| Architecture | 64-bit, x64-based PC |
| Shell | PowerShell |
| Build tool | Maven Wrapper, Apache Maven 3.10.0 |
| Baseline revision | `a54bee4e0179bd8a0de61e843c1b7e65df4bb46a` |
| Baseline run | November 8, 2026, approximately 3:07–3:08 PM (UTC−06:00) |

**Prerequisites:**
- JDK 21 or later.
- Git.
- PowerShell or another terminal.
- A clone of the repository at the baseline revision.
- The existing test keystore: `core/src/test/resources/actuator-test-keystore.p12`.

**Verify Java installation:**

```powershell
java -version
javac -version
```

**Verify Maven Wrapper:**

```powershell
.\mvnw.cmd -version
```

### Steps to Reproduce

The reproduction used two regression tests, one for each affected class. Both tests start a local JDK `HttpsServer` configured to accept only TLS 1.3.

**Step 1: Inspect the original production implementation**

From the repository root, inspect the SSL context creation in both classes:

```powershell
Select-String -Path `
  core\src\main\java\dev\jvmmcp\core\spring\ActuatorClient.java, `
  core\src\main\java\dev\jvmmcp\core\spring\ActuatorProbe.java `
  -Pattern 'SSLContext\.getInstance\("TLSv1\.2"\)'
```

Both classes originally contained:

```java
SSLContext context = SSLContext.getInstance("TLSv1.2");
```

**Step 2: Prepare the TLS 1.3-only HTTPS server**

Both regression tests reuse the repository's existing `actuator-test-keystore.p12`:

- Format: PKCS12.
- Password: `changeit`.
- Alias: `test`.
- Certificate common name: `localhost`.
- Key: 2048-bit RSA.

Each test starts a local `com.sun.net.httpserver.HttpsServer` on the loopback address and an ephemeral port.

The server uses an `HttpsConfigurator` that restricts its enabled protocols:

```java
SSLParameters sslParams = getSSLContext().getDefaultSSLParameters();
sslParams.setProtocols(new String[] {"TLSv1.3"});
params.setSSLParameters(sslParams);
```

The request handler records the negotiated protocol using:

```java
((HttpsExchange) exchange).getSSLSession().getProtocol()
```

The handler runs only after a successful TLS handshake.

**Step 3: Reproduce the ActuatorProbe behavior**

Test file:

`core/src/test/java/dev/jvmmcp/core/spring/ActuatorProbeTest.java`

The test starts the TLS 1.3-only server, enables insecure mode, and calls `probePort()`.

Expected behavior: the probe returns the beans payload and the server records `TLSv1.3`.

Baseline result: the test fails because `probePort()` returns an empty `Optional`.

**Step 4: Reproduce the ActuatorClient behavior**

Test file:

`core/src/test/java/dev/jvmmcp/core/spring/ActuatorClientTest.java`

The test starts a TLS 1.3-only HTTPS health endpoint and constructs the client using the public two-argument constructor:

```java
ActuatorClient insecureClient =
    new ActuatorClient(
        "https://127.0.0.1:" + httpsPort,
        new ActuatorAuth(null, null, null, true));
```

Expected behavior: the client retrieves the health response and the server records `TLSv1.3`.

Baseline result: `fetchEndpoint()` throws a `RuntimeException` caused by `javax.net.ssl.SSLHandshakeException: Remote host terminated the handshake`.

**Step 5: Run both regression test classes**

From the repository root, with JDK 21 selected:

```powershell
.\mvnw.cmd -pl core "-Dtest=ActuatorProbeTest,ActuatorClientTest" test
```

**Step 6: Compare actual and expected behavior**

The test results show that the TLS 1.3-only HTTPS scenario fails against the original production implementation.

The client exposes the handshake exception. The probe returns an empty `Optional`, but its underlying connection exception was not captured.

### Reproduction Evidence

**Status: Completed — baseline reproduction accepted by `repro-check`.**

The baseline run was performed against revision:

```text
a54bee4e0179bd8a0de61e843c1b7e65df4bb46a
```

Production code was unchanged. Only `ActuatorProbeTest.java` and `ActuatorClientTest.java` were modified locally to add the regression tests. The test changes were uncommitted.

**Baseline test results:**

| Test class | Tests run | Failures | Errors | Result |
|---|---:|---:|---:|---|
| `ActuatorClientTest` | 42 | 0 | 1 | TLS 1.3 regression test errored |
| `ActuatorProbeTest` | 26 | 1 | 0 | TLS 1.3 regression test failed |
| **Total** | **68** | **1** | **1** | **BUILD FAILURE** |

**ActuatorClient error excerpt:**

```text
java.lang.RuntimeException: Error connecting to https://127.0.0.1:<port>/actuator/health: Remote host terminated the handshake
Caused by: javax.net.ssl.SSLHandshakeException: Remote host terminated the handshake
```

**ActuatorProbe failure excerpt:**

```text
Expecting Optional to contain:
  "{"contexts":{"application":{"beans":{"userService":{}}}}}"
but was empty.
```

**Maven summary:**

```text
[ERROR] Tests run: 68, Failures: 1, Errors: 1, Skipped: 0
[INFO] BUILD FAILURE
```

The full test output and stack trace are recorded in the local reproduction report, `repro-draft.md`. The evidence was checked against the Surefire reports for both test classes.

**Evidence preparation notes:**
- The output comes from one Maven run that finished at `2026-11-08T15:08:13-06:00`.
- The client stack trace was checked against its Surefire report.
- Maven startup, compilation, and resource-processing output before the test banner was excluded.
- Trailing whitespace was removed.
- Unrelated console text immediately before the results section was omitted and marked.
- The reproduction report's excerpt-preparation note records these edits.

**Reproduction commit:** `none` — the regression tests were uncommitted local changes during the baseline reproduction.

**Reproduction limitations:**
- The target is a local JDK `HttpsServer`, not a Spring Boot application, reverse proxy, or load balancer.
- The client exception differs from the issue's reported `no cipher suites in common` or `Received fatal alert: protocol_version` wording.
- No `javax.net.debug` output or server-side log was captured to identify the server's exact reason for closing the handshake.
- The probe returns an empty `Optional`; its underlying exception was not captured.
- The TLS 1.3 negotiated-protocol assertions were not reached because both regression tests failed before a successful connection.
- TLS 1.2 control tests were added and run during post-fix verification, not during the baseline reproduction.

**Toolkit:**
- Used Claude Code's `repro-check` skill to grade the claim and reproduction report for issue #278.
- Improved the report based on evaluation feedback, including complete reproduction steps, test code, environment details, and actual Maven/Surefire evidence.
- Corrected the probe finding to distinguish the observed empty `Optional` from an unconfirmed underlying exception.
- Compared the client stack trace and test results against the Surefire reports.
- Ran the final `repro-check` evaluation. **Environment, steps, behavior_shown, honesty, and comms all passed. The final verdict was ACCEPT.**
- Kept the accepted reproduction report locally for use in the pull request description. It was not posted as a separate issue comment.

## Solution Approach

### Analysis

The source inspection identified two independent calls to:

```java
SSLContext.getInstance("TLSv1.2");
```

One is in `ActuatorClient`, and the other is in `ActuatorProbe`.

Both custom SSL contexts are used when insecure mode is enabled. The probe and client have separate responsibilities, so both paths needed to be addressed.

The existing tests also contained a mock that expected `"TLSv1.2"`. That expectation needed to be updated when the production code changed to request the generic `"TLS"` protocol.

### Proposed Solution

Replace the explicit TLS 1.2 protocol request with:

```java
SSLContext.getInstance("TLS");
```

This allows the Java runtime to negotiate a supported TLS protocol rather than explicitly requesting TLS 1.2. On a compatible Java 21 runtime and server, this allows TLS 1.3 negotiation.

The existing trust-all certificate manager and insecure-mode behavior remain unchanged.

### Implementation Plan

The plan was evaluated using plan-check and accepted after addressing its feedback.

1. Update SSL context creation in `ActuatorClient.java`.
2. Update SSL context creation in `ActuatorProbe.java`.
3. Update the corresponding mock expectation in `ActuatorProbeTest.java`.
4. Retain the TLS 1.3-only HTTPS regression test for `ActuatorProbe`.
5. Retain the TLS 1.3-only HTTPS regression test for `ActuatorClient`.
6. Add TLS 1.2-only control tests for both classes.
7. Run the focused tests.
8. Run the repository formatting check.
9. Run complete Maven verification.
10. Review the final diff and commit the focused implementation.

**Plan-check result:** ACCEPT. All eight checks passed after revising the plan to include the updated mock expectation and TLS 1.2 control tests.

**Implementation deviation:** The client test helper was generalized from `startTls13OnlyHttpsHealthServer()` to `startHttpsHealthServer(String protocol)` so the same helper could exercise both TLS 1.3 and TLS 1.2. The probe test helper was also generalized to accept enabled protocols. These changes support the accepted test plan and do not change production behavior.

**Toolkit:**
- Used Claude Code to trace the production code and identify the smallest proposed change.
- Used plan-check to evaluate diagnosis, scope, files, implementation approach, tests, risks, evidence uncertainty, and comment conventions.
- Revised the plan based on its findings before implementation.

## Testing Strategy

### Focused Tests

The focused regression test command was:

```powershell
.\mvnw.cmd -pl core "-Dtest=ActuatorProbeTest,ActuatorClientTest" test
```

**Result: PASS**

| Test class | Tests run | Result |
|---|---:|---|
| `ActuatorProbeTest` | 27 | Passed |
| `ActuatorClientTest` | 43 | Passed |
| **Total** | **70** | **All passed** |

The tests cover TLS 1.3-only and TLS 1.2-only local HTTPS endpoints for both the probe and client. They verify successful responses and the negotiated protocol.

### Formatting

Command:

```powershell
.\mvnw.cmd spotless:check
```

**Result: PASS.** The formatting check completed successfully.

### Full Verification

Command:

```powershell
.\mvnw.cmd clean verify
```

**Result: BUILD SUCCESS.**

| Module | Tests | Failures | Errors |
|---|---:|---:|---:|
| `core` | 595 | 0 | 0 |
| `mcp-server` | 469 | 0 | 0 |

Four tests were skipped because Docker was unavailable in the environment. No test failures or errors were reported.

### Regression Coverage

- Verified that the probe can discover an Actuator endpoint served over TLS 1.3 when insecure mode is enabled.
- Verified that the client can connect to an Actuator endpoint served over TLS 1.3 when insecure mode is enabled.
- Added TLS 1.2-only control coverage for both classes.
- Verified the negotiated protocol in the local HTTPS test servers.
- Updated the existing Mockito expectation to match `SSLContext.getInstance("TLS")`.
- Preserved the existing test coverage and insecure-mode behavior.

## Implementation Notes

The implementation changed the protocol selection in both production classes:

```java
SSLContext.getInstance("TLSv1.2");
```

was replaced with:

```java
SSLContext.getInstance("TLS");
```

The following files were changed:

1. `core/src/main/java/dev/jvmmcp/core/spring/ActuatorClient.java`
   - Changed the SSL context protocol request to `"TLS"`.

2. `core/src/main/java/dev/jvmmcp/core/spring/ActuatorProbe.java`
   - Changed the SSL context protocol request to `"TLS"`.

3. `core/src/test/java/dev/jvmmcp/core/spring/ActuatorClientTest.java`
   - Retained TLS 1.3 regression coverage.
   - Generalized the HTTPS test server helper to accept a protocol.
   - Added TLS 1.2 control coverage.
   - Verified the negotiated protocol and health response.

4. `core/src/test/java/dev/jvmmcp/core/spring/ActuatorProbeTest.java`
   - Updated the Mockito expectation to `"TLS"`.
   - Retained TLS 1.3 regression coverage.
   - Added TLS 1.2 control coverage.
   - Generalized the HTTPS server helper to support protocol selection and record the negotiated protocol.

The existing trust-all manager, SSL initialization, exception handling, and insecure-mode behavior were preserved. The client’s existing insecure warning was also preserved.

No unrelated production refactoring was introduced. No README change was required because this change did not alter documented constants, configuration defaults, limits, or resource bounds.

**Implementation commit:**

```text
0570361 fix(spring): allow TLS protocol negotiation for insecure actuator connections
```

The commit was created on branch:

```text
fix/278-actuator-tls-negotiation
```

The commit contains exactly the four Java production and test files listed above. The working tree was clean after the commit.

## Pull Request

**Status:** Pending.

The implementation is committed locally, but the pull request has not yet been created.

After implementation and verification, the following work remains:

1. Complete the contribution story and review its evidence.
2. Prepare a concise pull request description explaining the problem, implementation, and verification.
3. Include the accepted reproduction evidence and post-fix test results in the PR description.
4. Create the pull request from `fix/278-actuator-tls-negotiation` to the repository's target branch.
5. Link the pull request to issue #278.
6. Use PR-Monitor to track the pull request and document its results.

The repository requests focused pull requests, passing verification, and Conventional Commit messages with a lowercase scope. The implementation commit follows the requested format.

## Learnings and Reflections

This issue helped me understand how Java SSL context configuration affects TLS protocol negotiation.

I learned that explicitly requesting a protocol such as TLS 1.2 can restrict the connection, while requesting the generic `"TLS"` protocol allows the Java runtime to negotiate a compatible supported version.

I also learned the importance of tracing every code path that implements the same behavior. Updating only `ActuatorClient` would have left `ActuatorProbe` with the original restriction.

The reproduction process taught me to distinguish between an observed test result and an assumed underlying cause. For example, the client test exposed an `SSLHandshakeException`, while the probe test only returned an empty `Optional`. I also learned to compare test output against the actual Surefire reports instead of relying on manually edited excerpts.

The plan-check process helped me identify missing test coverage before implementation. Adding TLS 1.2 controls alongside the TLS 1.3 regression tests made the intended behavior more explicit.

The ship-gate process taught me to define repository-specific verification commands and use a hook to prevent outgoing PR commands from proceeding without text to check.

The issue-selection process taught me that finding a beginner-friendly issue is not enough. I need to verify recent repository activity, check for existing pull requests, confirm the issue is still available, and make sure I have enough time to reproduce and complete it.

My implementation and local verification are complete. My remaining work is to finalize the contribution story, create the pull request, and complete the PR-Monitor workflow.

## Resources Used

- [JVM-MCP repository](https://github.com/oscarbol09/jvm-mcp)
- [Issue #278 – TLS 1.3 support](https://github.com/oscarbol09/jvm-mcp/issues/278)
- [Open Source Issue Tracker](https://open-source-issue-tracker.onrender.com/)
- Repository `CONTRIBUTING.md`
- Java 21 documentation
- Claude Code and the Issue-Scout, repro-check, plan-check, voice-guard, and ship-gate skills
