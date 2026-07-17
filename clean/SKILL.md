---
name: clean
description: Audit a codebase for abstraction-driven, behavior-preserving architectural cleanup. Use for repo-wide cleanup review, abstraction and symmetry review, API and settings simplification, dead-code or legacy-cruft review, and maintainability audits; identify discrete improvements without editing code so the user can decide what to implement.
---

# Clean

## Outcome

Make the codebase conceptually smaller: easier to understand and change, with fewer accidental concepts, branches, and maintenance sites, while preserving live behavior and meaningful performance.

Correctness is necessary but not sufficient. Treat abstraction as a structural regularizer: a good abstraction gives each concept one clear owner, narrows the space of reasonable implementations, and makes local code predictable from the architecture.

Follow `AGENTS.md`, `CLAUDE.md`, and project conventions. Ground decisions in current code, call paths, artifacts, and tests—not generic clean-code doctrine.

Treat tentative ideas from discussion as hypotheses to verify against live structure, not contracts to propagate. Explicit user decisions remain authoritative.

## Workflow

1. Establish the user's relevant requirements and, for every shared-refactor candidate, a compact invariant ledger covering the concept and owner, canonical representation, genuine variants, stable behavior and artifact oracles, and maintenance sites removed.
2. Survey broadly enough to understand the architecture, then prioritize the smallest root-cause opportunities with the highest maintenance payoff. Trace entrypoints, call chains, registries, read/write paths, settings, config flow, and representative tests; do not infer ownership from filenames alone. Leave already-clear code alone.
3. Find duplicated decisions, unclear ownership, forced symmetry, redundant indirection, accidental concepts, pass-through arguments, duplicated defaults, and config choices callers never vary—not merely similar-looking text.
4. Form each candidate around a domain concept and classify every affected API or config value: compute derived facts, move stable shared policy to the canonical settings owner, and expose only genuine caller or experiment choices. Identify the owner, maintenance sites removed, genuine differences preserved, and equivalence check.
5. Present candidates one by one without editing. For each, state the evidence, relevant prior requirement, smallest proposed change, settings/API effect, predicted concept delta, preserved behavior, parity validation, trade-off, and confidence.
6. Re-read each proposal and calculate its concept delta across public types, registries, arguments, config keys, branches, and ownership sites. By default discard candidates that add more concepts or explanation than they remove, and do not turn settings into a dumping ground.
7. Stop after the complete, independently selectable audit list and wait for the user to decide. Do not implement, update tests, or modify docs or config.

## Abstraction Gate

Keep an abstraction when it compresses real repetition or symmetry, encodes policy, or protects a meaningful seam. Prefer abstractions that:

- give one domain concept one obvious owner and remove repeated maintenance decisions;
- expose genuine shared structure while keeping genuine differences visible;
- make callers and future extension paths simpler and predictable;
- lower cognitive load and, usually, total code size.

Collapse redundant indirection, including forwarding-wrapper chains, generic option bags, sentinel shims, ownerless registries, forced uniform interfaces, and small DSLs without real recurrence. Trace wrappers to the first implementation that does work; an intermediate layer earns its place only by owning policy or lifecycle, adapting representation or protocol, or eliminating repeated caller logic. Required framework hooks are not forwarding wrappers merely because they are short. Do not add helpers merely to move code or rename an interface.

Line-count reduction is evidence, not the objective. Growth must earn its cost through a clearer contract, meaningful validation, or a real protected seam. After implementation, a reader should be able to predict where related behavior belongs and how the next case should be added; otherwise the abstraction has not compressed the design enough.

Reject a proposed public type, registry, argument, metadata field, or config key unless evidence shows that it cannot be safely derived, serves multiple current consumers or a real policy seam, and removes invalid states or repeated maintenance decisions.

The primary agent owns canonical contracts and shared abstractions. During audit, subagents may only gather bounded evidence; after later user approval, they may implement only contracts already fixed by the primary agent.

## Cleanup Scope

- Remove dead code, stale compatibility paths, redundant indirection, duplicate policy, and unused configuration when live usage shows they earn no place.
- Leave deterministic input validation to the lowest existing owner: do not duplicate downstream library checks or pin their error text in repository tests; add repository validation only for stricter contracts, accepted but semantically invalid states, or failures delayed until after side effects or artifact publication.
- Delete only tautological, duplicate, or low-signal tests already superseded by stronger coverage. Preserve tests for semantics, parsing, data handling, public seams, repository-owned failure modes, and real regressions.
- Remove historical narration, inner-monologue comments, and comments that restate code. Preserve comments that explain invariants, contracts, coupling, failure modes, or non-obvious intent.
- Keep imports, constants, configuration declarations, and module-owned registries near the top when conventions allow. Do not hoist runtime side effects or dependency initialization merely for layout.
- Write ordinary documentation as natural Markdown paragraphs rather than one sentence per line. Preserve line-oriented structure where it carries meaning.
- Favor readable semantic units over clever density, speculative generality, or defensive scaffolding without a demonstrated need.

## Boundaries

- Preserve outputs, metrics, public contracts, side effects, artifact layouts, and material performance. Cleanup must not smuggle in feature or semantic changes.
- If a stronger design requires intentional behavior change, separate it as a proposal and explain the trade-off.
- Keep scientific or product behavior changes separate from architecture cleanup, and prioritize behavioral and artifact parity over tests that snapshot a new implementation.
- This skill is audit-only: inspect and report without editing code, config, tests, or docs. Present each candidate separately and leave implementation to a later explicit user decision.

## Completion

Finish when the user has a prioritized, fully evidenced, independently selectable list of cleanup candidates. Report what each candidate would make conceptually smaller, why future changes would be more predictable, how behavior would be preserved, and any remaining uncertainty.
