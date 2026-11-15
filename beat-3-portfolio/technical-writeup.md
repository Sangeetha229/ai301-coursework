# Fixing TLS 1.3 Connections in JVM-MCP Actuator Clients

## Overview

I contributed a fix to [JVM-MCP](https://github.com/oscarbol09/jvm-mcp), an open-source Java project, for [issue #278](https://github.com/oscarbol09/jvm-mcp/issues/278). The issue affected `ActuatorClient` and `ActuatorProbe`. Both explicitly requested a TLS 1.2 SSL context for insecure Actuator HTTPS connections, which prevented them from connecting to endpoints configured to support only TLS 1.3.

I updated the SSL context configuration in both classes, revised the related tests, and addressed a security scan finding discovered during review. My final change uses an explicit TLS 1.3 context. The maintainer confirmed that it supports TLS 1.3 while maintaining TLS 1.2 fallback on JDK 21 and later.

I submitted the changes in [PR #286](https://github.com/oscarbol09/jvm-mcp/pull/286). The maintainer approved and merged the pull request on November 14, 2026.

## What happened

### Finding and investigating the issue

I used Issue-Scout to search and evaluate open-source issues against my Java development interests and contribution preferences. The scan covered nine queries and 641 unique candidates, with five accepted for further evaluation. Issue #278 was a focused bug involving Java SSL configuration and tests, so I selected it.

I reviewed the affected production classes, their tests, and the repository contribution guidelines. I then used a local JDK HTTPS server configured to support TLS 1.3 only to reproduce the reported behavior.

Before changing the production code, I ran the focused tests against the original implementation. The baseline had 68 tests, with one failure and one error. `ActuatorClientTest` encountered an `SSLHandshakeException`, while `ActuatorProbeTest` returned an empty result instead of the expected payload. These results gave me a reproducible starting point for the fix.

### Planning and initial implementation

I used Plan-Check to review the implementation approach before making the code changes. The intended scope was limited to the SSL context configuration in `ActuatorClient` and `ActuatorProbe`, together with the corresponding tests.

My first implementation changed the SSL context algorithm from `TLSv1.2` to generic `TLS` in both production classes. I also updated the tests to cover TLS 1.2 and TLS 1.3 endpoints and generalized the HTTPS test helper to accept a protocol parameter.

The focused tests passed with this implementation. However, the security scan identified a blocking Semgrep finding for a weak SSL context. This was an important turning point: passing functional tests did not mean the implementation had satisfied the repository's security requirements.

### Revising the implementation after the security finding

I reviewed the Semgrep result and revised both production classes to request `TLSv1.3` explicitly instead of using generic `TLS`. I updated the related test expectation and retained the existing insecure certificate-trust behavior and warnings.

The final implementation was committed as:

- Commit: `ff484ee27340bb13b7185c3ee218a65f4435efa8`
- Message: `fix(spring): use explicit TLS 1.3 context for insecure actuator connections`

After this revision, the focused test suites passed:

- `ActuatorProbeTest`: 27 tests passed.
- `ActuatorClientTest`: 43 tests passed.
- Total: 70 tests passed, with zero failures and zero errors.

### Running the project checks

I also ran the full local Maven verification. The results were:

- Core module: 595 tests passed.
- MCP server module: 469 tests passed.
- Four Docker-dependent tests were skipped because Docker was unavailable locally.
- Spotless formatting validation passed.
- JaCoCo checks passed.
- `git diff --check` passed.
- Maven verification completed with `BUILD SUCCESS`.

I used Ship-Gate to check the contribution before opening the pull request. My first manifest attempt exposed a Windows Maven Wrapper command-path issue. I corrected the manifest to use `.\mvnw.cmd` and reran the checks successfully. My direct development and testing commands were run in Git Bash using `./mvnw`.

### Opening and monitoring the pull request

I used PR-Precheck to review contribution readiness and Voice-Guard to help keep the GitHub communication clear and accurate. I opened [PR #286](https://github.com/oscarbol09/jvm-mcp/pull/286) against the upstream repository.

I used PR-Monitor to track the pull request's checks, review decision, and activity. During the waiting period, the monitor showed that the pull request was mergeable but blocked, with no approval yet. I did not treat mergeability as approval, and I did not post the monitor's drafted wait note.

On November 14, the maintainer confirmed that the revised TLS 1.3 configuration resolved the SAST finding, maintained TLS 1.2 fallback on JDK 21 and later, and passed the TLS 1.2/1.3 integration test matrix. The maintainer also confirmed that CI was green across all runners.

PR #286 was approved and merged on November 14, 2026.

## Limitations and what's next

This change is limited to the SSL context used by the insecure Actuator connection paths in `ActuatorClient` and `ActuatorProbe`. It does not redesign the project's broader HTTP client configuration or change the default TLS behavior for connections that do not use the insecure authentication path.

My local reproduction used a JDK HTTPS server rather than a complete Spring Boot Actuator deployment. The maintainer's review provided additional confirmation of TLS 1.2 fallback on JDK 21 and later and successful TLS 1.2/1.3 integration tests in CI. The four Docker-dependent tests were also skipped in my local environment because Docker was unavailable.

A useful next step would be to continue validating the behavior across supported JDK versions and real Spring Boot Actuator deployments, particularly where TLS configuration is controlled by the target service. This would provide additional coverage beyond the local HTTPS test fixture and the integration tests confirmed by the maintainer.

This contribution taught me to treat functional testing, security analysis, and code review as connected parts of software development. When the first implementation passed functional tests but failed the security scan, I used that feedback to revise the solution rather than stopping at the first passing test run.