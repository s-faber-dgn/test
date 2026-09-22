---
name: implementing-quarkus-bce
description: Implement Quarkus features, behavior fixes, and focused architectural refactors using this repository's BCE conventions.
---

# Implementing Quarkus with BCE

1. Read the BCE, Java, testing, and agent ownership rules in the root
   [AGENTS.md](../../../AGENTS.md). Identify the business capability and inspect
   nearby production code and tests before choosing a package.
2. Map the requested behavior to HTTP handling, application orchestration, and
   domain state or rules. Decide whether a control adds actual behavior; use the
   smallest coherent implementation permitted by the repository's dependency rules.
3. State any changed HTTP contract or configuration requirement. Keep the change
   within the requested capability and assigned files; preserve existing behavior
   outside that scope.
4. Implement and add focused behavioral coverage. Use
   [testing-quarkus](../testing-quarkus/SKILL.md) when selecting or diagnosing tests.
5. Review imports and dependencies against the BCE rules. Run the relevant checks
   from AGENTS.md, coordinating Maven access with the parent when delegated.
   Report behavior changes, validation evidence, and any remaining blockers.
