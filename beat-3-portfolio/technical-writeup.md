Fixing TLS 1.3 Connections in JVM-MCP Actuator Clients
Overview
I contributed a fix to JVM-MCP, an open-source Java project, for issue #278.

The issue affected ActuatorClient and ActuatorProbe. Both explicitly requested a TLS 1.2 SSL context for insecure Actuator HTTPS connections. This prevented them from connecting to endpoints configured to support only TLS 1.3.

I updated the SSL context configuration in both classes, revised the related tests, and addressed a security scan finding discovered during review. The final implementation explicitly requests a TLS 1.3 context. The maintainer confirmed that it supports TLS 1.3 while maintaining TLS 1.2 fallback on JDK 21 and later.

I submitted the changes in PR #286. The maintainer approved and merged the pull request on November 14, 2026.

What happened
1. Finding and investigating the issue
I used Issue-Scout to search and evaluate open-source issues against my Java development interests and contribution preferences. The scan covered nine queries and 641 unique candidates, with five accepted for further evaluation.

Issue #278 was a focused bug involving Java SSL configuration and tests, so I selected it.

I reviewed the affected production classes, their tests, and the repository contribution guidelines. I then used a local JDK HTTPS server configured to support TLS 1.3 only to reproduce the reported behavior.

Before changing the production code, I ran the focused tests against the original implementation. The baseline had 68 tests, with one failure and one error:

ActuatorClientTest: 42 tests, with one error.
ActuatorProbeTest: 26 tests, with one failure.
The Actuator client error included:

javax.net.ssl.SSLHandshakeException:
Remote host terminated the handshake

The probe test returned an empty result instead of the expected payload.

These results gave me a reproducible starting point for the fix.

2. Planning and initial implementation
I used Plan-Check to review the implementation approach before making code changes. The intended scope was limited to the SSL context configuration in ActuatorClient and ActuatorProbe, together with the corresponding tests.

The original implementation explicitly requested TLS 1.2:

SSLContext.getInstance("TLSv1.2");

My first implementation changed both production classes to use the generic TLS algorithm:

SSLContext.getInstance("TLS");

I also updated the tests to cover TLS 1.2 and TLS 1.3 endpoints and generalized the HTTPS test helper to accept a protocol parameter.

The focused tests passed with this implementation. However, the security scan identified a blocking Semgrep finding for a weak SSL context.

3. Revising the implementation after the security finding
The CI Security & Secret Scan job failed during Semgrep SAST.

The finding identified both production locations:

core/src/main/java/dev/jvmmcp/core/spring/ActuatorClient.java:162
core/src/main/java/dev/jvmmcp/core/spring/ActuatorProbe.java:69

java.lang.security.audit.weak-ssl-context.weak-ssl-context (Blocking)

An insecure SSL context was detected.

This was an important turning point. Passing functional tests did not mean the implementation had satisfied the repository's security requirements.

I reviewed the finding and revised both production classes to request TLSv1.3 explicitly instead of using generic TLS.

The final change was:

// Original implementation
SSLContext.getInstance("TLSv1.2");

// First implementation
SSLContext.getInstance("TLS");

// Final implementation
SSLContext.getInstance("TLSv1.3");

I also updated the related test expectation and retained the existing insecure certificate-trust behavior and warnings.

The final implementation was committed as:

Commit: ff484ee27340bb13b7185c3ee218a65f4435efa8
Message: fix(spring): use explicit TLS 1.3 context for insecure actuator connections
4. Testing the revised implementation
After the security-related revision, both focused test suites passed:

Test suite	Tests passed
ActuatorProbeTest	27
ActuatorClientTest	43
Total	70
There were zero failures and zero errors.

I also ran the full local Maven verification:

Verification	Result
Core module	595 tests passed
MCP server module	469 tests passed
Docker-dependent tests	4 skipped because Docker was unavailable
Spotless formatting	Passed
JaCoCo checks	Passed
git diff --check	Passed
Maven verification	BUILD SUCCESS
I used Ship-Gate to check the contribution before opening the pull request. My first manifest attempt exposed a Windows Maven Wrapper command-path issue. I corrected the manifest to use .\mvnw.cmd and reran the checks successfully. My direct development and testing commands were run in Git Bash using ./mvnw.

5. Opening and monitoring the pull request
I used PR-Precheck to review contribution readiness and Voice-Guard to help keep GitHub communication clear and accurate.

I opened PR #286 against the upstream repository.

I used PR-Monitor to track the pull request's checks, review decision, and activity. During the waiting period, the monitor showed that the pull request was mergeable but blocked, with no approval yet. I did not treat mergeability as approval, and I did not post the monitor's drafted wait note.

During maintainer review, the TLS configuration and security scan finding were revisited. The maintainer confirmed:

It correctly allows negotiation of TLS 1.3 while maintaining fallback to TLS 1.2 on JDK 21+

The maintainer also confirmed that the security finding was resolved, the TLS 1.2 and TLS 1.3 integration test matrix passed, and CI was green across all runners.

PR #286 was approved and merged on November 14, 2026.

6. Limitations and what's next
This change is limited to the SSL context used by the insecure Actuator connection paths in ActuatorClient and ActuatorProbe. It does not redesign the project's broader HTTP client configuration or change the default TLS behavior for connections that do not use the insecure authentication path.

My local reproduction used a JDK HTTPS server rather than a complete Spring Boot Actuator deployment. The maintainer's review provided additional confirmation of TLS 1.2 fallback on JDK 21 and later and successful TLS 1.2/1.3 integration tests in CI.

Four Docker-dependent tests were skipped in my local environment because Docker was unavailable.

A useful next step would be to continue validating the behavior across supported JDK versions and real Spring Boot Actuator deployments, particularly where TLS configuration is controlled by the target service. This would provide additional coverage beyond the local HTTPS test fixture and the integration tests confirmed by the maintainer.

What I learned
This contribution taught me to treat functional testing, security analysis, and code review as connected parts of software development.

The first implementation passed functional tests but failed the security scan. Rather than stopping at the first passing test run, I reviewed the finding and revised the implementation.

I also learned to distinguish between a pull request being technically mergeable and actually being approved. Monitoring the review process helped me communicate accurately and avoid making assumptions about maintainer decisions.

Most importantly, I learned that AI-assisted development can support issue discovery, investigation, implementation planning, testing, and contribution management, but engineering judgment remains essential at every stage.

Contribution: JVM-MCP PR #286
Original issue: JVM-MCP Issue #278
Status: Approved and merged — November 14, 2026
