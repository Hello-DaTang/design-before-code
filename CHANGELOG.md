# Changelog

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
