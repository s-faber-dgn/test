# pulse

This project uses Quarkus, the Supersonic Subatomic Java Framework.

If you want to learn more about Quarkus, please visit its website: <https://quarkus.io/>.

## Running the application in dev mode

You can run your application in dev mode that enables live coding using:

```shell script
./mvnw quarkus:dev
```

> **_NOTE:_**  Quarkus now ships with a Dev UI, which is available in dev mode only at <http://localhost:8080/q/dev/>.

## Packaging and running the application

The application can be packaged using:

```shell script
./mvnw package
```

It produces the `quarkus-run.jar` file in the `target/quarkus-app/` directory.
Be aware that it’s not an _über-jar_ as the dependencies are copied into the `target/quarkus-app/lib/` directory.

The application is now runnable using `java -jar target/quarkus-app/quarkus-run.jar`.

If you want to build an _über-jar_, execute the following command:

```shell script
./mvnw package -Dquarkus.package.jar.type=uber-jar
```

The application, packaged as an _über-jar_, is now runnable using `java -jar target/*-runner.jar`.

## Creating a native executable

You can create a native executable using:

```shell script
./mvnw package -Dnative
```

Or, if you don't have GraalVM installed, you can run the native executable build in a container using:

```shell script
./mvnw package -Dnative -Dquarkus.native.container-build=true
```

You can then execute your native executable with: `./target/pulse-1.0.0-SNAPSHOT-runner`

If you want to learn more about building native executables, please consult <https://quarkus.io/guides/maven-tooling>.

## Related Guides

- REST ([guide](https://quarkus.io/guides/rest)): Build RESTful web services and APIs using Jakarta REST (formerly JAX-RS)

## Provided Code

### REST

Easily start your REST Web Services

[Related guide section...](https://quarkus.io/guides/getting-started-reactive#reactive-jax-rs-resources)

## Repository workflow

Use JDK 25 and the checked-in Maven wrapper (3.9.16). Maven Enforcer checks the
runtime and compile/runtime dependency convergence. Install local Git hooks with
`npm ci`; commitlint checks Conventional Commit messages and pre-push checks Java.

| Command | Purpose |
| --- | --- |
| `./mvnw spotless:apply` | Repair Java formatting; review the diff |
| `./mvnw spotless:check checkstyle:check` | Run the same non-mutating checks as pre-push |
| `./mvnw test` | Run regular tests |
| `./mvnw verify` | Enforce build requirements, lint, format, packaging and regular tests |
| `./mvnw verify -DskipITs=false` | Full submission/release verification, including packaged tests |

Spotless owns Java formatting (AOSP style); Checkstyle owns selected naming and
structural rules. Both plugins and their engines are pinned in `pom.xml`.
Generated output is outside the checked source roots. Hooks are local feedback,
not a security boundary: bypassing a hook does not disable Maven's checks, but no
CI or remote release gate is configured yet. Run full verification before releases.

[AGENTS.md](AGENTS.md) is the authoritative reference for BCE architecture, coding
rules, Git Flow, and release versions. Task branches use `feat/`, `fix/`, or
`chore/` from `develop` and squash back with a Conventional Commit. Release and
hotfix integration preserves merge ancestry. Tags identify releases; Maven release
versions match their tags, while develop carries a provisional next-minor snapshot.
Version selection is maintainer-operated; release automation is not installed.

## Codex skills and agents

Repository skills are discovered from `.agents/skills`. They describe procedures;
project rules remain in AGENTS.md. Examples:

- `$implementing-quarkus-bce add a customer lookup endpoint`
- `$testing-quarkus add failure-case coverage for customer lookup`
- `$working-with-git-flow prepare the next release locally`
- `$committing-with-commitlint write a commit message for this change`

Project agents live in `.codex/agents`: `bce_reviewer` performs read-only analysis,
`quarkus_implementer` edits assigned implementation files, and `quarkus_tester`
edits assigned test files. Unspecified model and permission settings inherit from
the parent; the reviewer explicitly requests a read-only sandbox. For example:

> Review the customer component with bce_reviewer while inspecting its test gaps.

The parent delegates only useful independent work, assigns non-overlapping writable
files, and owns shared configuration and final validation. File ownership is an
instruction contract rather than a per-file sandbox. All Maven runs are serialized
because even focused tests share `target/`. Subagents do not delegate further.
Start a fresh Codex session after changing project instructions/configuration.

References: [BCE](https://bce.design/),
[Codex skills](https://learn.chatgpt.com/docs/build-skills), and
[custom agents](https://learn.chatgpt.com/docs/agent-configuration/subagents).
