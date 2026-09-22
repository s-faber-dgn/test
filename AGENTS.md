# Repository Guidelines

## Project and architecture

Pulse is a Maven-based Quarkus REST service on Java 25. Production and test sources
live in `src/main/java/org/dgn` and `src/test/java/org/dgn`; packages match paths.
Runtime configuration belongs in `src/main/resources/application.properties`.
Dockerfiles live in `src/main/docker`; generated artifacts belong in `target/`.

Follow [BCE](https://bce.design/) with business-first packages:
`org.dgn.<business-component>.boundary`, `.control`, and `.entity`. A component
names a business capability, not a framework layer. The greeting starter may stay
in its existing package until a feature warrants moving it.

- Boundary: HTTP or other external entry points, input validation and mapping.
- Control: business orchestration and stateless business logic.
- Entity: domain concepts, state, and persistence mappings when needed.
- Allow boundary → control → entity and boundary → entity for simple operations.
- Forbid control → boundary and entity → control/boundary dependencies. Reverse
  notifications use events when needed, not reverse dependencies.
- Cross-component interaction goes through boundary or control APIs; avoid direct
  access to another component's entities. Avoid boundary-to-boundary orchestration.
- Create only needed layers: no empty packages or pass-through controls.

## Code and formatting

Use standard Java naming: UpperCamelCase types, lowerCamelCase methods/variables,
UPPER_SNAKE_CASE constants, and lowercase packages. Prefer constructor injection,
explicit imports, focused REST resources, and domain names over generic services.

Spotless owns Java whitespace, wrapping, and imports, using google-java-format
AOSP style (four spaces). Checkstyle owns naming, braces, and top-level type
structure, not formatting. `.editorconfig` provides editor basics only.
Formatter exclusions must be explicit and narrowly scoped. Generated sources are
never formatted or linted; normal Maven output is already outside checked roots.
Do not add broad generated-directory exclusions or blanket formatter escapes.

## Build and tests

Use `./mvnw` (Maven 3.9.16); Enforcer requires Java 25 and Maven 3.9.16–3.x.
Keep the Quarkus BOM authoritative for dependency versions. Investigate convergence
failures instead of masking them with broad exclusions.

- `./mvnw quarkus:dev`: live reload and Dev UI at http://localhost:8080/q/dev/.
- `./mvnw spotless:apply`: repair formatting; inspect the resulting diff.
- `./mvnw spotless:check checkstyle:check`: non-mutating Java checks.
- `./mvnw test`: regular tests during development.
- `./mvnw verify`: formatting, lint, Enforcer, packaging, and regular tests.
- `./mvnw verify -DskipITs=false`: required final submission/release verification,
  including packaged integration tests (plain verify skips these by default).
- `./mvnw package`: JVM application in `target/quarkus-app/`.
- `./mvnw package -Dnative`: native build with GraalVM; add
  `-Dquarkus.native.container-build=true` for container builds.

Add/update tests for behavior changes. Use JUnit 5 with descriptive method names,
plain unit tests for domain logic, `@QuarkusTest` with REST Assured for HTTP
behavior, and `@QuarkusIntegrationTest` for packaged behavior. Name tests `*Test`
and packaged tests `*IT`, mirroring production packages. Do not invent behavior
changes or tests for mechanical formatting.

Hooks provide local feedback; Maven verification is independent of hooks.
Pre-push runs style checks once and fails on violations. Hooks can be bypassed;
there is currently no CI or remote release gate. Full verification before release
is a workflow requirement, not a remotely enforced guarantee.

## Commits and pull requests

Follow `commitlint.config.js` and the
[commitlint skill](.agents/skills/committing-with-commitlint/SKILL.md).
Use Conventional Commits, e.g. `feat(customer): add synchronization`. Scope is
optional. Validate messages and correct hook failures rather than bypassing them.
Standard Git merge messages are exempt from commitlint. Keep mechanical formatting
in a separate `style(java): normalize Java formatting` commit.
PRs explain motivation, behavior, configuration/API changes, and test evidence;
link relevant issues. Include screenshots only for visible UI changes.

## Git Flow and versions

Use ordinary Git commands; no Git Flow extension is required.

| Branch | Start from | Integrate into | Integration |
| --- | --- | --- | --- |
| `feat/<description>` | `develop` | `develop` | Squash |
| `fix/<description>` | `develop` | `develop` | Squash |
| `chore/<description>` | `develop` | `develop` | Squash |
| `release/<version>` | `develop` | `main`, then `develop` | Merge commits |
| `hotfix/<description>` | `main` | `main`, then `develop` | Merge commits |

Descriptions use lowercase kebab-case. `main` represents released code; `develop`
integrates upcoming work. A task's Conventional Commit squash message is its
release-relevant record; intermediate branch commits do not determine versions.
Release/hotfix merges preserve ancestry and use clear standard messages such as
`Merge release/1.4.0 into main`. Back-merges also preserve ancestry. Integrate a
hotfix into any active release branch before shipping that release.

Git tags are authoritative release identifiers. A maintainer owns release
preparation: set the Maven project version to match `release/<version>` and tag
the verified main merge commit `v<version>`. Release and main versions have no
`-SNAPSHOT`. Do not derive versions independently from branch names or changelogs.

Determine a release from changes since the previous released tag: breaking
changes (`!` or BREAKING CHANGE) require major, `feat` minor, and `fix` patch;
take the largest bump. Other types do not independently trigger a release.
The initial planned release is 1.0.0, matching the existing development snapshot.

After a release, develop uses a provisional next-minor snapshot: 1.4.0 becomes
1.5.0-SNAPSHOT. Actual changes may require adjusting that target at release
preparation. When back-merging, restore the development snapshot before completing
the merge. Hotfixes increment the released patch version and preserve the current
development or active-release version during back-merges. A breaking change or new
feature belongs in a normal release, not a patch hotfix.

Future release automation must implement this policy, ignore merge commits, and
exclude changes already reachable from the preceding release tag (including
previously released hotfixes). Derive any changelog from that same change set.
Release automation is not installed; documentation does not authorize publishing.

## Agent collaboration

Delegate bounded independent work when useful; handle small tasks locally.
Use project agents `bce_reviewer`, `quarkus_implementer`, and `quarkus_tester`.
Consult relevant procedural skills under `.agents/skills`; this file remains the
source of project rules.

- The parent assigns explicit writable files and interfaces before delegation.
  No two concurrent agents may own overlapping writable files.
- Reviewers do not edit. Implementers edit assigned implementation files only;
  tests require explicit assignment. Testers edit assigned test files only.
- Only the parent may edit outside delegated ownership, including shared build
  and agent configuration. Coordinate ownership transfers before edits.
- Ownership is an instruction contract, not a per-file filesystem sandbox.
- Subagents do not delegate further. Return changed files, findings, validation,
  and blockers to the parent without committing or merging.
- Serialize all Maven commands in the shared checkout, including focused tests
  and formatting. Obtain the parent's scheduling agreement before running Maven.
  The parent owns final repository-wide verification and integration.

## Configuration and security

Never commit credentials, local `.env` files, or `target/`. Keep environment values
outside source control and document new required configuration keys in the PR.
