# Fixing a TLS 1.3 Connection Issue in an Open-Source Java Project

I fixed a TLS 1.3 connection issue in **JVM-MCP**, an open-source Java project, enabling its Actuator clients to negotiate modern TLS protocols when connecting to secured services.

Throughout this contribution, I used Claude Code and custom AI-assisted workflows to identify a suitable issue, investigate and reproduce the problem, plan the implementation, validate the changes, and prepare the pull request.

One important lesson came during the review process. Although my initial implementation passed the functional tests, a Semgrep security scan identified a weak SSL context configuration. I replaced the generic TLS context with an explicit TLS 1.3 context, addressed the Semgrep finding, and validated the revised solution with TLS 1.2 and TLS 1.3 integration tests.

**The contribution was approved and merged on November 14, 2026 (PR #286).** The final implementation addressed the Semgrep security finding, and the TLS 1.2 and TLS 1.3 integration tests passed. The pull request's CI checks also completed successfully.

This experience reinforced an important lesson: AI-assisted development is not just about generating code. It can support the entire open-source contribution lifecycle, while engineering judgment, security awareness, reproducible testing, and collaboration remain essential.

🔗 Pull request: https://github.com/oscarbol09/jvm-mcp/pull/286
