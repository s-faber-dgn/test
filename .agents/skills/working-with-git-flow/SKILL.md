---
name: working-with-git-flow
description: Select and integrate task branches, prepare releases, and handle hotfixes using this repository's Git Flow and Maven version lifecycle.
---

# Working with Git Flow

Read the Git workflow and release/version ownership rules in the root
[AGENTS.md](../../../AGENTS.md). Use
[committing-with-commitlint](../committing-with-commitlint/SKILL.md) when writing
commit messages. Repository rules determine branch names, merge modes, and versions.

## Task work

1. Inspect `git status`, the current branch, local/remote branch availability, and
   relevant history. Preserve existing changes and identify the correct base from
   AGENTS.md before creating or integrating a branch.
2. For integration, review the complete branch diff and validation evidence.
   Compose the squash message from the resulting behavior, including breaking
   changes, rather than copying intermediate messages.
3. Carry out the requested integration with the documented merge mode. Inspect
   the resulting history and worktree, and report what was integrated.

## Releases and hotfixes

1. Identify the previous release tag and changes not reachable from it. Apply the
   documented SemVer policy to release-relevant commits; inspect merge ancestry
   so back-merges cannot count an already released change twice.
2. Establish the maintainer's target version and compare it with the calculated
   bump. Resolve a material mismatch before preparing the release. Update the
   Maven project version consistently with the release identity in AGENTS.md.
3. Run the required release verification before integration or tagging. Review
   the exact commit and version that will become the release.
4. Integrate and tag only within the user's authorized scope. Follow the documented
   back-merge and development snapshot rules; inspect version conflicts explicitly
   rather than blindly accepting either branch's `pom.xml`.
5. Report completed local actions, the resulting versions and history, and any
   remaining publication steps. A request to prepare a release does not itself
   authorize pushing or publishing it.
