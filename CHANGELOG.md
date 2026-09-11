# Changelog

This repository contains independently versioned core Skills. The early `v0.1` / `v0.2` / `v0.2.1` history below records the evolution of `data-model-design`; those entries were **not** repository-wide release tags.

A repository-wide release version will be introduced with the first public alpha. Until then, the version declared inside each Skill is the canonical version for that Skill.

## Unreleased — repository alpha preparation

### Current core Skill versions

- `business-domain-design` — v0.2.2
- `ux-flow-design` — v0.2.1
- `data-model-design` — v0.2.3
- `design-readiness-review` — v0.2

### Added

- Four-Skill greenfield design loop: Business → UX → Data → cross-artifact Readiness.
- Optional OpenSpec `design-before-code` schema with a human-only approval boundary.
- Project Charter, Method Synthesis, Evaluation Protocol, Roadmap, installation guidance, and agent-compatibility documentation.
- Focused regression definitions for the compact v0.2.x correctness fixes discovered during realistic validation.
- First Level 3 benchmark evidence: Employee Training Request + Training Expense Reimbursement.
- Compact end-to-end documentation example under `examples/quickstart/`.

### Changed

- `business-domain-design v0.2.2` adds mandatory reconciliation after a human resolves a previously open consequential decision.
- `ux-flow-design v0.2.1` makes unresolved consequential choices uninstantiated and adds reachability / gate-algebra review for exception flows.
- `data-model-design v0.2.3` strengthens dual-path identity, stored-derived-value, and reference+snapshot integrity review.
- `design-readiness-review` remains v0.2 after correctly detecting and routing cross-artifact reconciliation and reachability defects during the Level 3 benchmark.
- README and OpenSpec integration documentation now reflect completed Phase C runtime/approval validation and completed Phase D Level 3 readiness validation.

### Validation evidence

- Phase B cross-artifact smoke: PASS.
- Phase C OpenSpec orchestration: PASS at the current experimental level, including negative/positive semantic approval-gate behavior and 9/9 planning artifacts without `apply`.
- Phase D Level 3 domain benchmark: final `READINESS: READY_FOR_HUMAN_APPROVAL`; no implementation performed.

### Release posture

The project is experimental / alpha-stage. Current work is release hardening: documentation, installation clarity, interoperability claims, packaging consistency, and release hygiene. Another expensive Level 3 benchmark is not required before the first alpha unless a material method change invalidates existing evidence.

---

## `data-model-design` v0.2.1 — 2026-09-10

This patch release was driven by review of the v0.2 manufacturing regression run.

### Added

- Declarative-integrity review before claiming a duplicated/transitively derived fact cannot be enforced by the database.
- Composite FK + composite UNIQUE as an explicit integrity pattern to consider when appropriate to the target database.
- Mutable-fact semantics that distinguish:
  - prospective business change;
  - retroactive correction;
  - original-applied preservation;
  - intentional latest/current-value reinterpretation.
- Valid/effective time vs recording/transaction-time distinction when both "what was true then?" and "what did the system believe then?" matter.
- Canonical-unit review separating UI/input units from storage/computation units.
- Regression evals for declarative redundancy enforcement, mutable-fact semantics, and canonical units.
- `docs/PROJECT-CHARTER.md` as an anti-drift source of truth for mission, scope, product invariants, anti-overfitting rules, and iteration protocol.

### Changed

- Redundancy review no longer implies automatic redundancy removal. Intentional redundancy may remain when it serves a concrete need and its invariant is explicit and sufficiently enforceable.
- Temporal modeling no longer treats effective-dated history as the generic answer to any mutable value.
- Unit examples in requirements are no longer treated as an implicit canonical physical representation when calculations/precision depend on the choice.

### Motivation

The v0.2 run successfully caught transitive redundancy, late-entry business-time errors, snapshot terminology, unresolved current-version semantics, and lifecycle boundaries. Review then exposed three deeper weaknesses:

1. It incorrectly implied some duplicated FK relationships could not be protected declaratively by a relational database, overlooking composite FK/key designs.
2. It still risked collapsing prospective change, retroactive correction, and intentional latest-value reinterpretation into one generic "history" solution.
3. It improved unresolved-unit handling but did not make the canonical computation/storage unit explicit enough as a modeling decision.

v0.2.1 generalizes those failures while preserving the project's core goal: expose consequential design choices for human review before implementation.

## `data-model-design` v0.2 — 2026-09-10

This release was driven by an A/B evaluation of the same manufacturing requirement with and without `data-model-design`.

### Added

- Decision provenance categories: FACT, INFERENCE, RECOMMENDATION, ASSUMPTION, DECISION REQUIRED.
- Mandatory transitive/derived redundancy review.
- Business-effective time vs system recording time.
- Precise distinction between current identity reference, historical relationship reference, value snapshot, and effective-dated history.
- Adversarial scenario simulation for late entry and backdated correction.
- Physical-model decision lock: unresolved consequential business decisions cannot be silently finalized in schema fields.
- Regression evals for:
  - `product_id + product_version_id` transitive redundancy;
  - employee transfer followed by late historical entry;
  - foreign-key identity mislabeled as a full snapshot;
  - premature `current_version_id` selection;
  - unresolved storage units finalized in physical fields;
  - different lifecycles hidden inside one record;
  - recommendations mislabeled as requirements.

### Changed

- `data-model-design` now treats the physical model as candidates when consequential semantics are unresolved.
- Scenario simulation now distinguishes business-effective and recording timestamps.
- Manufacturing example now demonstrates redundancy contradiction and late-entry temporal failure.

### Motivation

The v0.1 skill already improved human reviewability, scenario simulation, and explicit decision gates, but the A/B test showed that a strong agent could still:

- miss transitively duplicated facts;
- copy current state at insertion time instead of reconstructing event-time truth;
- call a stored foreign key a "snapshot" even when mutable labels remain live;
- embed an unresolved recommendation in the physical model;
- say a unit was undecided while still choosing a concrete physical representation.

v0.2 turns those observed failures into explicit rules and regression tests.

## `data-model-design` v0.1 — 2026-09-10

Initial experimental release of `data-model-design` with:

- conceptual → logical → physical modeling;
- temporal review;
- scenario simulation;
- design review checklist;
- human review gate;
- manufacturing example and initial eval suite.
