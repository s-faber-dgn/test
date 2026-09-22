---
name: testing-quarkus
description: Add or diagnose unit, Quarkus HTTP, and packaged integration tests for this repository, including verification of behavior changes.
---

# Testing Quarkus

Read the testing, build, and agent ownership rules in the root
[AGENTS.md](../../../AGENTS.md), then inspect the relevant tests and Maven
configuration.

Choose the lightest test that exercises the changed behavior:

- Plain JUnit for domain logic that does not require CDI or a running application.
- `@QuarkusTest` for CDI-dependent behavior; use REST Assured for HTTP contracts,
  checking status, response content, and relevant headers.
- `@QuarkusIntegrationTest` for behavior that must hold in the packaged application.
  Reuse HTTP assertions where suitable; avoid assuming access to in-process CDI.

Cover the requested outcome and meaningful failure cases instead of mirroring
implementation details. Follow repository naming and package conventions. When
delegated, request production fixes from the parent rather than editing outside
the assigned test files.

Coordinate Maven access before running even focused tests. Use
`./mvnw -Dtest=ExampleTest test` for a selected regular test. Follow AGENTS.md for
full validation; the parent owns repository-wide verification. For failures,
inspect `target/surefire-reports` or `target/failsafe-reports`, distinguish assertion
failures from startup or environment problems, and report the command and evidence.
Do not turn off checks to obtain a passing result.
