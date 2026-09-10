# Data Model Review Checklist

Run this checklist before declaring a design ready for implementation.

## Decision provenance

- [ ] Consequential statements are distinguishable as FACT, INFERENCE, RECOMMENDATION, ASSUMPTION, or DECISION REQUIRED.
- [ ] Recommendations are not presented as already-approved business rules.
- [ ] Framework defaults are not mistaken for domain requirements.
- [ ] Every DECISION REQUIRED item names the model elements it blocks.
- [ ] No blocked decision has already been silently committed in the physical model.

## Business meaning

- [ ] Every entity has a clear real-world meaning.
- [ ] Every entity has a reason to exist independently.
- [ ] Important business terms are defined consistently.
- [ ] Tables are not derived merely from pages, forms, DTOs, or API payload shapes.
- [ ] Speculative abstractions are identified and challenged.
- [ ] Concepts with materially different lifecycles have not been incorrectly collapsed into one entity.

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
- [ ] Multiple stored relationship paths cannot contradict each other without detection.
- [ ] Before declaring an invariant "not enforceable by the database," target-database options such as composite FKs/UNIQUE keys, CHECKs, generated columns, partial/exclusion constraints, or alternate key design were considered.

## Redundancy and dependency walk

For every duplicated or derivable fact ask why it exists.

- [ ] Duplicate fields are classified as value snapshot, historical relationship reference, denormalization/cache, integration copy, derived value, or mistake.
- [ ] A source of truth is known for every intentional duplicate.
- [ ] Updating one record cannot silently create contradictory business facts.
- [ ] Convenience duplication has been challenged.
- [ ] Transitive dependencies have been traced, not just duplicate column names.
- [ ] For each FK or business attribute, the reviewer asked whether it is derivable through another stored relationship.
- [ ] If both a derived value and its source are stored, the consistency mechanism and concrete benefit are documented.
- [ ] Intentional redundancy was evaluated rather than automatically rejected when it supports a real query, partitioning, integration, uniqueness, or performance need.

Example regression check:

`production_record.product_version_id -> product_version.product_id`

If `production_record.product_id` is also stored, the design must justify why both are necessary and explain how inconsistent combinations are prevented. A composite FK may be a valid enforcement mechanism when supported by the target schema/database.

## Temporal correctness

- [ ] Consequential mutable facts have explicit historical semantics.
- [ ] Business-effective time is distinguished from system recording time.
- [ ] Late entry and backdated correction have been considered where relevant.
- [ ] Each important mutation is classified as prospective business change, retroactive correction, original-applied preservation, or intentional latest/current-value reinterpretation.
- [ ] Effective-dated history is not used automatically when the business actually wants latest-value reinterpretation.
- [ ] Retroactive correction is not accidentally modeled as a prospective change.
- [ ] Historical records cannot be unintentionally rewritten by updating reference/master data.
- [ ] If historical rewriting is intentional, that policy is explicitly stated rather than treated as a defect.
- [ ] A stored foreign key is not mislabeled as a full value snapshot when mutable labels still come from current master data.
- [ ] Version/history tables preserve actual historical semantics rather than only a `version` label.
- [ ] Audit timestamps are not being mistaken for full historical reconstruction.
- [ ] If both valid-time truth and prior system-belief state matter, both dimensions are explicitly considered.
- [ ] Organizational relationships, prices/rates, standards, classifications, and versions are checked at the correct business-effective time.

## Lifecycle and deletion

- [ ] Physical delete, soft delete, deactivate, archive, and supersede semantics are chosen deliberately.
- [ ] Soft delete is not added only because a framework supports it.
- [ ] Referenced historical records remain understandable after current entities are retired.

## Physical design

- [ ] Column types reflect actual domains and ranges.
- [ ] Input/display units are distinguished from canonical storage/computation units.
- [ ] If canonical unit affects type, precision, rounding, constraints, or integration, it is explicitly decided or marked BLOCKED.
- [ ] Indexes are justified by known access patterns, joins, sorting, or uniqueness.
- [ ] Uniqueness is enforced at the database level where appropriate.
- [ ] JSON is not replacing stable relational structure without reason.
- [ ] Generic `status`, `type`, or key-value structures have explicit semantics.
- [ ] Audit/version columns are justified, not ritualistically added.
- [ ] A physical field/type/unit is not finalized while its governing business decision is unresolved.
- [ ] If two materially different physical representations remain valid, both are shown or the affected detail is marked blocked.

## Scenario validation

- [ ] At least one realistic end-to-end scenario has been simulated.
- [ ] The simulation includes a later change to mutable reference data.
- [ ] When relevant, the simulation includes an adversarial timing case: event occurs → master data changes → event is entered late or corrected backdated.
- [ ] At least one scenario tests whether a change is prospective, corrective, original-applied, or latest-value reinterpretation when that distinction matters.
- [ ] The model still produces the expected business answer after that change.
- [ ] Contradictions found during the scenario have been resolved or surfaced for human decision.

## Human review gate

The design is **not ready** when any unresolved item can materially change:

- entity identity;
- table boundaries;
- cardinality;
- historical semantics;
- uniqueness;
- deletion behavior;
- canonical type/unit representation;
- user-visible business meaning.

List those items explicitly instead of selecting an arbitrary implementation.
