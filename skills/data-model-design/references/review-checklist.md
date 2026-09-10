# Data Model Review Checklist

Run this checklist before declaring a design ready for implementation.

## Business meaning

- [ ] Every entity has a clear real-world meaning.
- [ ] Every entity has a reason to exist independently.
- [ ] Important business terms are defined consistently.
- [ ] Tables are not derived merely from pages, forms, DTOs, or API payload shapes.
- [ ] Speculative abstractions are identified and challenged.

## Identity and relationships

- [ ] Durable entities have stable identity independent of mutable labels.
- [ ] Cardinality is explicit for important relationships.
- [ ] Ownership/lifecycle responsibility is clear.
- [ ] Many-to-many relationships have been checked for hidden business meaning.
- [ ] Orphan records are prevented or intentionally allowed.

## Integrity

- [ ] Required values are non-null where appropriate.
- [ ] Natural uniqueness rules are enforced where appropriate.
- [ ] Foreign keys are present where relational integrity matters.
- [ ] Important invariants are not left solely to application code without reason.
- [ ] Legal state values and state transitions are understood.

## Redundancy

For every duplicated fact ask why it exists.

- [ ] Duplicate fields are classified as snapshot, denormalization/cache, integration copy, derived value, or mistake.
- [ ] A source of truth is known for every intentional duplicate.
- [ ] Updating one record cannot silently create contradictory business facts.
- [ ] Convenience duplication has been challenged.

## Temporal correctness

- [ ] Consequential mutable facts have been classified as current reference, snapshot, or effective-dated history.
- [ ] Historical records cannot be unintentionally rewritten by updating reference/master data.
- [ ] Version/history tables preserve actual historical semantics rather than only a `version` label.
- [ ] Audit timestamps are not being mistaken for full historical reconstruction.

## Lifecycle and deletion

- [ ] Physical delete, soft delete, deactivate, archive, and supersede semantics are chosen deliberately.
- [ ] Soft delete is not added only because a framework supports it.
- [ ] Referenced historical records remain understandable after current entities are retired.

## Physical design

- [ ] Column types reflect actual domains and ranges.
- [ ] Indexes are justified by known access patterns, joins, sorting, or uniqueness.
- [ ] Uniqueness is enforced at the database level where appropriate.
- [ ] JSON is not replacing stable relational structure without reason.
- [ ] Generic `status`, `type`, or key-value structures have explicit semantics.
- [ ] Audit/version columns are justified, not ritualistically added.

## Scenario validation

- [ ] At least one realistic end-to-end scenario has been simulated.
- [ ] The simulation includes a later change to mutable reference data.
- [ ] The model still produces the expected historical result after that change.
- [ ] Contradictions found during the scenario have been resolved or surfaced for human decision.

## Human review gate

The design is **not ready** when any unresolved item can materially change:

- entity identity;
- table boundaries;
- cardinality;
- historical semantics;
- uniqueness;
- deletion behavior;
- user-visible business meaning.

List those items explicitly instead of selecting an arbitrary implementation.
