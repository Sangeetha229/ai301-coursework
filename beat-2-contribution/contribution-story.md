# Contribution Story: Enable TLS 1.3 Negotiation for Insecure Actuator Connections

**Repository:** [oscarbol09/jvm-mcp](https://github.com/oscarbol09/jvm-mcp)  
**Issue:** [#278 – TLS 1.3 support for insecure Actuator HTTPS connections](https://github.com/oscarbol09/jvm-mcp/issues/278)  
**Pull Request:** Pending  
**Contribution Number:** 1

## Toolkit

### Unit 5 – Issue-Scout

I developed and improved an `issue-scout` skill in Claude Code to search for and evaluate beginner-friendly open-source issues. I refined its search strategy, candidate-selection rubric, and evaluation criteria through repeated testing. The skill considers my Java experience, contribution preferences, issue scope, repository activity, maintainer activity, and existing work. The latest evaluation achieved **20/20 on the scored evaluation items**.

### Unit 6 – Voice-Guard and Repository Memory

I developed a voice guard using Claude Code hooks and `check_voice.py` to check outgoing GitHub comments and prevent messages that violate my communication guidelines. I also created a local `CLAUDE.local.md` file to preserve repository-specific contribution rules, build commands, and issue-related findings across Claude Code sessions. I centralized my voice guidance and configured a `PreToolUse` hook so that communication rules can be checked before supported GitHub actions.

### Unit 7 – Ship-Gate and Plan-Check

**Status:** Setup and plan review completed. Implementation and final verification are pending.

I installed the `ship-gate` skill in my local Claude Code configuration and configured its `PreToolUse` hook alongside the existing voice-guard hook. The installer created a `ship-manifest.md` in the target repository and added the local working files to `.git/info/exclude`.

I documented the repository's required pre-PR checks:
- `.\mvnw.cmd spotless:check`
- `.\mvnw.cmd clean verify`

I ran both checks before implementation. Spotless passed. The baseline `clean verify` failed in the two TLS 1.3 regression tests added for reproduction; these failures are part of the issue being investigated and are not excluded from the required checks.

I also tested the ship-gate hook using the assignment's `gh pr create --help` command. It stopped the command because no PR title and body file were supplied, confirming that the hook was active. The voice-guard hook had also been verified during the earlier PowerShell test.

I used the `plan-check` skill to evaluate `plan.md` and the local `plan-comment.md` draft for issue #278. The first evaluation was rejected because the plan did not account for an affected Mockito expectation or explicit TLS 1.2 compatibility controls. I revised the plan to address both findings. The final evaluation returned **ACCEPT**, with all eight checks passing. The plan and draft comment remain local; I did not post the draft comment on the issue.

### Unit 8 – PR-Monitor

**Status:** Pending. I will document the Unit 8 toolkit and its use during the pull request process after completing that work.

## Why I Chose This Issue

I searched for beginner-friendly Java issues using GitHub search commands and the Issue-Scout skill I developed in Claude Code. The skill uses my experience, preferences, search criteria, and evaluation rubric to identify issues that are relevant to my background and manageable in scope.

I also explored the [Open Source Issue Tracker](https://open-source-issue-tracker.onrender.com/) to find suitable contribution opportunities. However, I found that many beginner-friendly issues were stale. Some of the issues I claimed had their claim comments deleted, and in a few cases another contributor opened a pull request after I had started working on the issue. Since I could not always complete an issue immediately due to limited time, these experiences taught me the importance of checking recent activity, verifying existing work, and selecting issues that I can realistically investigate and complete.

The latest Issue-Scout scan evaluated **641 unique candidates**. Five issues were accepted from the full evaluations, and this issue was selected as a good match for my Java backend experience. The skill rated it as a straightforward, focused bug fix.

I chose issue #278 because it identifies a specific problem in two Java classes: `ActuatorClient` and `ActuatorProbe`. Both use a hardcoded TLS 1.2 protocol, which can prevent connections to servers that require TLS 1.3. The proposed change appears limited and provides an opportunity to learn more about Java SSL configuration, TLS protocol negotiation, and testing secure connections.

The Issue-Scout helped narrow down the search, but I still needed to verify the issue's behavior through reproduction and inspect the existing implementation before making changes.

**Claim comment:** Posted on November 7, 2026. The comment identifies the planned TLS 1.3-only reproduction and asks whether another contributor is already working on the issue.

**Claim comment link:** [View posted claim comment](https://github.com/oscarbol09/jvm-mcp/issues/278#issuecomment-6711994926)

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

Both classes use:

```java
SSLContext.getInstance("TLSv1.2");
```

This explicitly requests TLS 1.2 rather than allowing the Java runtime to negotiate a supported TLS version. As a result, a server that only permits TLS 1.3 may not be reachable through these insecure HTTPS paths.

### Expected Behavior

When insecure HTTPS connections are enabled, the Actuator client and probe should be able to negotiate a compatible TLS protocol, including TLS 1.3 when supported by both the Java runtime and the server.

The existing insecure-connection behavior, including its trust-all certificate handling and security warnings, should remain intact.

### Current Behavior

Source inspection identified that both `ActuatorClient` and `ActuatorProbe` explicitly request `TLSv1.2` when creating their custom insecure SSL contexts.

The baseline reproduction against a TLS 1.3-only local HTTPS server confirmed that:

- The `ActuatorClient` regression test throws an `SSLHandshakeException`.
- The `ActuatorProbe` regression test receives an empty `Optional` instead of the expected beans payload. Its underlying exception was not captured.

The client error message differs from the wording reported in the original issue. The reproduction details and limitations are documented below.

### Affected Components

1. **`ActuatorClient.java`**
   - Creates the HTTP client used to retrieve Actuator health, metrics, and startup information.
   - Its insecure HTTPS path creates a custom SSL context using TLS 1.2.

2. **`ActuatorProbe.java`**
   - Discovers Actuator endpoints by checking candidate ports and requesting `/actuator/beans`.
   - Its insecure HTTPS path also creates a custom SSL context using TLS 1.2.

3. **`ActuatorProbeTest.java`**
   - Contains HTTPS discovery tests and a test that mocks `SSLContext.getInstance("TLSv1.2")`.
   - The mock expectation must be updated to match the proposed generic `"TLS"` protocol request.

4. **`ActuatorClientTest.java`**
   - Contains tests for insecure client construction.
   - Includes TLS 1.3-specific HTTPS regression coverage, with TLS 1.2 compatibility coverage planned.

**Toolkit:**
- Used Claude Code to inspect the repository and trace the SSL context creation paths.
- Reviewed the relevant production classes and existing tests.
- Used the repository's `CONTRIBUTING.md` to understand project conventions and testing requirements.

## Reproduction Process

### Environment Setup

The baseline reproduction was performed on the original production implementation at revision `a54bee4`. The regression tests were added locally to the two test classes; production code remained unchanged.

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

Both classes contain:

```java
private static SSLContext trustAllSslContext() {
    try {
        SSLContext context = SSLContext.getInstance("TLSv1.2");
        context.init(null, new TrustManager[] {new TrustAllManager()}, null);
        return context;
    } catch (GeneralSecurityException e) {
        throw new IllegalStateException("Unable to initialise insecure TLS context", e);
    }
}
```

**Step 2: Prepare the TLS 1.3-only HTTPS server**

Both regression tests reuse the repository's existing `actuator-test-keystore.p12`:

- Format: PKCS12.
- Password: `changeit`.
- Alias: `test`.
- Certificate common name: `localhost`.
- Key: 2048-bit RSA.

Each test starts a local `com.sun.net.httpserver.HttpsServer` on the loopback address and an ephemeral port.

The server uses a TLS SSL context and an `HttpsConfigurator` that restricts its enabled protocols:

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

The test starts the TLS 1.3-only server, enables insecure mode, and calls `probePort()`:

```java
@Test
@DisplayName("probePort discovers a TLS 1.3-only HTTPS actuator when insecure is enabled")
void shouldDiscoverTls13OnlyHttpsEndpointWhenInsecure() throws Exception {
    int httpsPort = startHttpsBeansServer("TLSv1.3");

    ActuatorProbe probe = new ActuatorProbe(new ActuatorAuth(null, null, null, true));
    Optional<String> json = probe.probePort(httpsPort);

    assertThat(json).contains(BEANS_PAYLOAD);
    assertThat(negotiatedProtocol.get()).isEqualTo("TLSv1.3");
}
```

Expected behavior: the probe returns the beans payload and the server records `TLSv1.3`.

Baseline result: the test fails because `probePort()` returns an empty `Optional`.

**Step 4: Reproduce the ActuatorClient behavior**

Test file:

`core/src/test/java/dev/jvmmcp/core/spring/ActuatorClientTest.java`

The test starts a TLS 1.3-only HTTPS health endpoint and constructs the client using the public two-argument constructor:

```java
@Test
void testInsecureAuthConnectsToTls13OnlyHttpsServer() throws Exception {
    int httpsPort = startTls13OnlyHttpsHealthServer();

    ActuatorClient insecureClient =
            new ActuatorClient(
                    "https://127.0.0.1:" + httpsPort,
                    new ActuatorAuth(null, null, null, true));

    assertThat(insecureClient.fetchEndpoint("/actuator/health"))
            .isEqualTo("{\"status\":\"UP\"}");
    assertThat(negotiatedProtocol.get()).isEqualTo("TLSv1.3");
}
```

Expected behavior: the client retrieves the health response and the server records `TLSv1.3`.

Baseline result: `fetchEndpoint()` throws a `RuntimeException` caused by `javax.net.ssl.SSLHandshakeException: Remote host terminated the handshake`.

**Step 5: Run both regression test classes**

From the repository root, with JDK 21 selected:

```powershell
.\mvnw.cmd -pl core "-Dtest=ActuatorProbeTest,ActuatorClientTest" test
```

The baseline run covered both test classes in one Maven invocation.

**Step 6: Compare actual and expected behavior**

The test results show that the TLS 1.3-only HTTPS scenario fails against the original production implementation.

The client exposes the handshake exception. The probe returns an empty `Optional`, but its underlying connection exception is not captured.

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

**Reproduction commit:** `none` — the regression tests are uncommitted local changes.

**Reproduction limitations:**
- The target is a local JDK `HttpsServer`, not a Spring Boot application, reverse proxy, or load balancer.
- The client exception differs from the issue's reported `no cipher suites in common` or `Received fatal alert: protocol_version` wording.
- No `javax.net.debug` output or server-side log was captured to identify the server's exact reason for closing the handshake.
- The probe returns an empty `Optional`; its underlying exception was not captured.
- The TLS 1.3 negotiated-protocol assertions were not reached because both regression tests failed before a successful connection.
- No TLS 1.2 control test or post-fix test has been run yet.

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

Both custom SSL contexts are used when insecure mode is enabled. The probe and client have separate responsibilities, so both paths need to be addressed.

The existing tests also contain a mock that expects `"TLSv1.2"`. That expectation must be updated if the production code changes to request the generic `"TLS"` protocol. Otherwise, the test may no longer intercept the SSL context creation.

The initial plan review also identified the need for explicit TLS 1.2-only compatibility tests. The existing TLS 1.3 regression tests alone would not demonstrate that the change preserves TLS 1.2 connections.

### Proposed Solution

Replace the explicit TLS 1.2 protocol request with:

```java
SSLContext.getInstance("TLS");
```

This allows the Java runtime to negotiate a supported TLS protocol rather than explicitly requesting TLS 1.2. On a compatible Java 21 runtime and server, this allows TLS 1.3 negotiation while retaining compatibility with TLS 1.2.

The existing trust-all certificate manager, SSL initialization, exception handling, and insecure-mode behavior should remain unchanged.

### Implementation Plan

The plan was reviewed and accepted by the `plan-check` skill. The approved implementation steps are:

1. Update the SSL context creation in `ActuatorClient.java` from `"TLSv1.2"` to `"TLS"`.
2. Update the SSL context creation in `ActuatorProbe.java` from `"TLSv1.2"` to `"TLS"`.
3. Update the corresponding Mockito expectation in `ActuatorProbeTest.java` from `"TLSv1.2"` to `"TLS"`, including the test `shouldThrowOnGeneralSecurityException()`.
4. Retain the TLS 1.3-only HTTPS regression test for `ActuatorProbe`.
5. Add a TLS 1.2-only HTTPS control test for `ActuatorProbe`, asserting successful discovery and negotiated protocol `TLSv1.2`.
6. Retain the TLS 1.3-only HTTPS regression test for `ActuatorClient`.
7. Add a TLS 1.2-only HTTPS control test for `ActuatorClient`, asserting a successful health response and negotiated protocol `TLSv1.2`.
8. Preserve the existing trust-all behavior, SSL initialization, exception handling, and warning.
9. Review the final changes and run the focused tests, formatting check, and full Maven verification.

No unrelated refactoring or changes to the duplicated helper methods are planned.

**Toolkit – Plan-Check:**
- Used Claude Code's `plan-check` skill to review `plan.md` and the draft `plan-comment.md` for issue #278.
- The first evaluation returned **REJECT**. It identified a Mockito expectation that needed updating and the absence of explicit TLS 1.2 compatibility controls.
- Revised the plan to include the Mockito update and TLS 1.2-only control tests for both affected classes.
- Updated the test plan, affected-file list, risks, and verification steps.
- Ran the plan-check evaluation again. **The final verdict was ACCEPT, with all eight checks passing.**
- The accepted plan is ready for implementation. The draft comment remains local and was not posted to the issue.

**Toolkit – Ship-Gate:**
- Installed the Unit 7 ship-gate files and configured the `PreToolUse` hook in the local Claude Code settings.
- Verified that the ship-gate hook stopped the test `gh pr create --help` command because it did not include the required PR title and body file.
- Created `ship-manifest.md` with the repository's required formatting and full verification commands.
- Kept local working files excluded through `.git/info/exclude`.

## Testing Strategy

### Focused Tests

Run both test classes together:

```powershell
.\mvnw.cmd -pl core "-Dtest=ActuatorProbeTest,ActuatorClientTest" test
```

They can also be run individually:

```powershell
.\mvnw.cmd -pl core -Dtest=ActuatorProbeTest test
```

```powershell
.\mvnw.cmd -pl core -Dtest=ActuatorClientTest test
```

### Formatting

Apply formatting if needed:

```powershell
.\mvnw.cmd spotless:apply
```

Check formatting:

```powershell
.\mvnw.cmd spotless:check
```

### Full Verification

```powershell
.\mvnw.cmd clean verify
```

### Regression Coverage

The implementation must verify:

- The probe can discover an Actuator endpoint served over TLS 1.3 when insecure mode is enabled.
- The client can connect to an Actuator endpoint served over TLS 1.3 when insecure mode is enabled.
- The probe can discover an Actuator endpoint served over TLS 1.2 when insecure mode is enabled.
- The client can connect to an Actuator endpoint served over TLS 1.2 when insecure mode is enabled.
- Existing self-signed HTTPS behavior remains covered.
- The existing exception-handling test uses the updated SSL context protocol expectation.

### Baseline Manifest Checks

Before implementation, I ran the two required checks recorded in `ship-manifest.md`:

| Check | Baseline result |
|---|---|
| `.\mvnw.cmd spotless:check` | **BUILD SUCCESS** — formatting check passed. |
| `.\mvnw.cmd clean verify` | **BUILD FAILURE** — the two TLS 1.3 regression tests failed against the original implementation. |

The full verification check remains required and will be rerun after implementation. The baseline TLS regression failures are not being treated as unrelated exclusions.

**Test Results:** Pending. The baseline reproduction results and pre-implementation manifest checks are complete, but implementation and post-fix verification have not yet been performed.

## Implementation Notes

- The project requires JDK 21 or later.
- The `core` module should remain pure Java and avoid introducing Spring, ORM, or JSON framework dependencies.
- Follow the existing project conventions and Java formatting rules.
- Keep the change limited to TLS protocol selection and the regression tests.
- Preserve the existing insecure-mode warning and trust-all certificate behavior.
- No README change is currently expected because the proposed change does not alter documented constants, defaults, limits, or resource bounds.
- The actual implementation and post-fix test results will be recorded after the changes are made.

## Pull Request

**Status:** Pending.

No pull request link is available yet.

After implementation and verification, I will:

1. Review the final diff.
2. Run the required formatting and verification commands.
3. Write a concise pull request description explaining the problem and solution.
4. Link the pull request to issue #278.
5. Include the accepted reproduction evidence and relevant post-fix test results in the PR description.

The repository requests focused pull requests, passing verification, and Conventional Commit messages with a lowercase scope.

## Learnings and Reflections

This issue helped me understand how Java SSL context configuration affects TLS protocol negotiation.

I learned that explicitly requesting a protocol such as TLS 1.2 can restrict the connection, while requesting the generic `"TLS"` protocol allows the Java runtime to negotiate a compatible supported version.

I also learned the importance of tracing every code path that implements the same behavior. Updating only `ActuatorClient` would leave `ActuatorProbe` with the original restriction.

The reproduction process taught me to distinguish between an observed test result and an assumed underlying cause. For example, the client test exposed an `SSLHandshakeException`, while the probe test only returned an empty `Optional`. I also learned to compare pasted test output against the actual Surefire reports instead of relying on manually edited excerpts.

The plan review reinforced the importance of testing both the new behavior and existing compatibility. A TLS 1.3 regression test demonstrates the requested improvement, while TLS 1.2-only controls help ensure that the change does not unintentionally break existing connections. It also helped me identify a Mockito expectation that would otherwise become inconsistent with the production change.

The ship-gate setup taught me how to define required repository checks and enforce them before attempting to create a pull request.

The issue-selection process taught me that finding a beginner-friendly issue is not enough. I need to verify recent repository activity, check for existing pull requests, confirm the issue is still available, and make sure I have enough time to reproduce and complete it.

My next steps are to implement the accepted plan, verify both TLS 1.3 regression tests and both TLS 1.2 control tests, and run the repository's formatting and full verification commands.

## Resources Used

- [JVM-MCP repository](https://github.com/oscarbol09/jvm-mcp)
- [Issue #278 – TLS 1.3 support](https://github.com/oscarbol09/jvm-mcp/issues/278)
- [Open Source Issue Tracker](https://open-source-issue-tracker.onrender.com/)
- Repository `CONTRIBUTING.md`
- Java 21 documentation
- Claude Code and the Issue-Scout, repro-check, plan-check, voice-guard, and ship-gate skills
