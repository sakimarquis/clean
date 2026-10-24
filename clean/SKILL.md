---
name: clean
description: Audit a repository for behavior-preserving cleanup that unifies concepts and terminology, consolidates shared logic, removes unnecessary code, abstractions, and tests, and restructures documentation into a layered hierarchy. Use for architecture, API, settings, duplication, dead-code, test-suite, documentation, and maintainability reviews; propose changes without implementing them.
---

# Clean

## Outcome

Make the repository easier to read, understand, and change while preserving live behavior and meaningful performance. Aim for a small, coherent design: few concepts, one name and one representation per concept, one owner per piece of shared logic, a test suite in which every test catches something no other test does, and documentation in which every fact lives in exactly one place. Fewer lines alone do not establish a better design; a proposal must reduce what a reader has to learn or what must change together.

Follow `AGENTS.md`, `CLAUDE.md`, and project conventions. Ground decisions in current code, call paths, artifacts, and tests. Verify tentative discussion ideas against the code; explicit user decisions remain authoritative.

## Workflow

1. Establish the user's requirements. Trace entrypoints, call chains, data and config flow, read/write paths, and representative tests to understand how the system connects. Do not infer ownership from filenames alone.
2. Inventory candidates in every area below: concepts, shared logic, code details, tests, and documentation. Use concrete user examples to identify recurring patterns, not blanket bans. Compare semantics, not just similar-looking text. Leave already-clear code alone.
3. For each candidate, identify the canonical owner, which callers or tests should reuse it, what differences must remain, and which behavior and artifacts must stay unchanged. Prefer the smallest change that fixes the cause.
4. Keep a proposal only if it actually reduces concepts, names, branches, arguments, config keys, tests, duplicated text, or places that must change together. Discard proposals that need more machinery or explanation than they remove unless a concrete benefit justifies the cost.
5. Report a prioritized list of independently selectable proposals grouped by area, including the area tables described below. For each proposal give the evidence, smallest change, benefit, affected APIs/settings/tests/docs, behavior-preservation check, and material trade-offs or uncertainty, mentioning only the fields that matter for that item.

## Concepts and Terminology

Each concept gets one name and one canonical representation across code, configuration, CLI flags, saved state, tests, and docs. Look for synonyms naming the same thing (`mode`/`variant`/`kind`), one name used for different things, parallel representations of one fact (a string tag plus an enum plus a boolean flag), and keywords, flags, config keys, or types that encode a distinction the code never acts on. Remove concepts that add no meaning, derive metadata from the canonical representation instead of maintaining copies, and keep real semantic differences visible under distinct names. Report a glossary table: concept, canonical name, aliases or redundant concepts to remove, and where they occur.

## Shared Logic and Abstractions

Give each shared behavior an obvious owner and route equivalent operations through the same implementation. Extend existing helpers and methods before adding parallel ones, and do not force unrelated behavior into one interface merely because the code looks similar.

An abstraction earns its place by sharing real logic, enforcing a meaningful contract, owning policy or lifecycle, or adapting a representation or protocol. Keep simple calculations visible; do not add helpers merely to move code or rename an interface. Prefer one parameterized implementation when variants differ only in a constant. Trace wrapper chains to the implementation that does work and remove layers that contribute nothing. Required framework hooks are not redundant merely because they are short.

Compute derived values instead of exposing them as options. Keep shared defaults and policy with their existing owner, and expose only genuine caller or experiment choices. Do not turn settings into a dumping ground. Add types, registries, metadata, or configuration only when current usage or a meaningful contract requires them and they prevent invalid states or repeated maintenance decisions.

Prefer direct, readable code over clever density, speculative generality, generic option bags, sentinel shims, or small DSLs without real recurring use. Scale validation, serialization, and file publication machinery to the actual artifact requirements. A reader should be able to follow what calls what, where decisions are made, and where related behavior belongs without relying on explanatory narration.

## Code Details

- Remove dead code, stale compatibility paths, duplicate policy, unused configuration, and redundant indirection when current usage shows they are unnecessary. Verify dependency workarounds against currently supported versions rather than historical comments.
- Keep input validation at the lowest existing owner. Do not duplicate library or upstream checks. Prefer native errors over catch-and-rethrow explanations; keep repository errors concise. Add repository checks only for stricter contracts, semantically invalid inputs the library accepts, or errors otherwise detected after side effects or artifact publication.
- Keep imports, constants, configuration declarations, and module-owned registries near the top when conventions allow. Do not move runtime side effects or dependency initialization just for layout.

## Tests

Judge the suite as a whole: a test earns its place by catching a regression no other test catches. Flag tests that only check config values, defaults, constants, trivial accessors, wrapper plumbing, incidental formatting, or error wording; tests and assertions subsumed by a broader test that exercises and asserts the same behavior; families of tests that differ only in inputs or rebuild the same scenario; and tests that duplicate logic, either by re-implementing production code to compute expected values or by copying setup and helpers across files. Derive expected values from known input/output pairs, invariants, or independent references, call production code rather than mirroring it, and share repeated setup through fixtures. Prefer fewer tests that each drive a meaningful path end to end and assert its important properties, so small units are covered through the behavior that uses them; parametrize only where inputs genuinely vary. Before calling a test redundant, confirm the covering test asserts the behavior rather than merely executing it. Preserve tests for semantics, parsing, data handling, public contracts, repository-owned failures, and real regressions. Report a table: test(s), verdict (delete, merge, or keep), covering or resulting test, and the coverage that remains.

## Documentation and Comments

Layer documentation by decreasing abstraction, stating each fact once at the most general level that needs it and letting readers reach details by `@path` references instead of copies. `AGENTS.md` gives agents the repository map: a one-sentence purpose, directory layout, entrypoints, commands, and conventions. The root `README.md` gives the fuller overview: what the project does, setup, usage, and how modules fit together. Each subdirectory's `README.md` holds the most detailed module information: responsibilities, interfaces, data formats, and caveats. Flag content duplicated across levels, placed at the wrong level, stale relative to the code, or front-loading details that a reference would reveal on demand.

Write every prose paragraph on a single line; never hard-wrap a paragraph across lines. Keep line-oriented structure such as lists, tables, and code blocks where it carries meaning. In docs and code comments alike, remove restatements of code and narration about history, design choices, ownership, or test setup that adds no operational information; preserve explanations of intent, invariants, contracts, coupling, failure modes, and non-obvious trade-offs. Report documentation proposals as a table: content, current location, and target location or deletion.

## Boundaries and Completion

This skill is audit-only: inspect and report without editing code, config, tests, or docs. Finish with the complete proposal list and leave implementation to an explicit user decision. If subagents are used, keep their assignments to gathering evidence; the primary agent owns shared contracts and abstractions, and any later delegated implementation must follow those agreed contracts.

Preserve outputs, metrics, public contracts, side effects, artifact layouts, and material performance. Validate proposed cleanup against existing behavior and artifacts, not tests that merely mirror a new implementation. If a design requires a scientific, product, or other intentional behavior change, present that separately and explain the trade-off.
