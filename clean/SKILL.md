---
name: clean
description: Audit a codebase for behavior-preserving cleanup that improves readability, unifies terminology and shared logic, clarifies component relationships, and removes unnecessary code and abstractions. Use for architecture, API, settings, duplication, dead-code, and maintainability reviews; propose changes without implementing them.
---

# Clean

## Outcome

Make the codebase easier to read, understand, and change, while preserving live behavior and meaningful performance. Use one clear term and one canonical representation for each concept; remove names, distinctions, and layers that add no meaning. Consolidate helpers and methods that implement the same behavior, reuse existing shared logic, and keep genuine differences explicit.

Make responsibilities, data flow, and connections between components apparent from the code itself. Aim for elegance through a small, coherent design: fewer concepts to learn, clear places to make changes, and no superfluous machinery.

Follow `AGENTS.md`, `CLAUDE.md`, and project conventions. Ground decisions in current code, call paths, artifacts, and tests. Verify tentative discussion ideas against the code; explicit user decisions remain authoritative.

## Workflow

1. Establish the user's requirements. Trace entrypoints, call chains, data and config flow, read/write paths, and representative tests to understand how the system connects. Do not infer ownership from filenames alone.
2. Find concepts with competing names or representations, helpers and methods that duplicate behavior, unclear responsibilities, redundant layers, unused code, and unnecessary caller choices. Compare semantics, not just similar-looking text. Leave already-clear code alone.
3. For each candidate, identify where the shared behavior belongs, which callers should reuse it, what differences must remain, and which behavior and artifacts must stay unchanged. Prefer the smallest change that fixes the cause.
4. Check whether the proposal actually reduces concepts, branches, arguments, config keys, or places that must change together. Discard proposals that require more machinery or explanation than they remove unless a concrete benefit justifies the cost.
5. Present a prioritized list of independently selectable proposals. For each, give the code evidence, smallest change, readability or maintenance benefit, affected APIs/settings, behavior-preservation check, and material trade-offs or uncertainty. Include relevant prior requirements without repeating a fixed checklist for every item.

## Shared Logic and Abstractions

Give each shared concept an obvious owner. Unify terminology across definitions and callers, and route equivalent operations through the same implementation. Extend existing helpers and methods before adding parallel ones. Keep real semantic differences visible; do not force unrelated behavior into one interface merely because the code looks similar.

An abstraction earns its place by sharing real logic, enforcing a meaningful contract, owning policy or lifecycle, or adapting a representation or protocol. Do not add helpers merely to move code or rename an interface. Trace wrapper chains to the implementation that does work and remove layers that contribute nothing. Required framework hooks are not redundant merely because they are short.

Compute derived values instead of exposing them as options. Keep shared defaults and policy with their existing owner, and expose only genuine caller or experiment choices. Do not turn settings into a dumping ground. Add types, registries, metadata, or configuration only when current usage or a meaningful contract requires them and they prevent invalid states or repeated maintenance decisions.

Prefer direct, readable code over clever density, speculative generality, generic option bags, sentinel shims, or small DSLs without real recurring use. Fewer lines alone do not establish a better design. A reader should be able to follow what calls what, where decisions are made, and where related behavior belongs without relying on explanatory narration.

If subagents are used, keep audit assignments limited to gathering evidence. The primary agent owns shared contracts and abstractions; any later delegated implementation must follow those agreed contracts.

## Cleanup Details

- Remove dead code, stale compatibility paths, duplicate policy, unused configuration, and redundant indirection when current usage shows they are unnecessary.
- Keep input validation at the lowest existing owner. Do not duplicate library checks or test their exact error wording. Add repository checks only for stricter contracts, semantically invalid inputs the library accepts, or errors otherwise detected after side effects or artifact publication.
- Remove only tautological or duplicate tests, or low-value tests superseded by stronger coverage. Preserve tests for semantics, parsing, data handling, public contracts, repository-owned failures, and real regressions.
- Remove historical narration, inner-monologue comments, and comments that restate code. Preserve explanations of intent, invariants, contracts, coupling, failure modes, and non-obvious trade-offs.
- Keep imports, constants, configuration declarations, and module-owned registries near the top when conventions allow. Do not move runtime side effects or dependency initialization just for layout.
- Write ordinary documentation as natural Markdown paragraphs. Preserve line-oriented structure where it carries meaning.

## Boundaries and Completion

This skill is audit-only: inspect and report without editing code, config, tests, or docs. Finish with the complete proposal list and leave implementation to an explicit user decision.

Preserve outputs, metrics, public contracts, side effects, artifact layouts, and material performance. Validate proposed cleanup against existing behavior and artifacts, not tests that merely mirror a new implementation. If a design requires a scientific, product, or other intentional behavior change, present that separately and explain the trade-off.
