# Changelog

## v0.2.1 — 2026-09-10

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

## v0.2 — 2026-09-10

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

## v0.1 — 2026-09-10

Initial experimental release of `data-model-design` with:

- conceptual → logical → physical modeling;
- temporal review;
- scenario simulation;
- design review checklist;
- human review gate;
- manufacturing example and initial eval suite.
