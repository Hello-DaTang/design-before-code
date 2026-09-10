# Modeling Principles

Use these principles when the main SKILL requires deeper reasoning.

## 1. Start from business meaning

Do not derive tables from screens, request DTOs, response JSON, controller methods, or form sections.

Ask first:

- What exists independently in the real world?
- What has identity over time?
- What is merely an attribute of something else?
- What is a relationship rather than an entity?
- What is an event or historical fact?

## 2. Every entity needs a reason to exist

For each entity, explain:

- what it represents;
- what gives it identity;
- what business rule requires it to persist independently;
- why its fields do not belong on another entity;
- who owns its lifecycle.

If these questions cannot be answered, reconsider the entity.

Also look for the opposite problem: two facts with different lifecycles may have been incorrectly collapsed into one entity. A plan that exists before execution may not be the same business concept as the eventual actual record.

## 3. Track decision provenance

Consequential model statements should be traceable to one of these origins:

- FACT — explicit business requirement;
- INFERENCE — logically derived from facts;
- RECOMMENDATION — a proposed design choice;
- ASSUMPTION — a provisional choice;
- DECISION REQUIRED — a human choice that materially changes the model.

Do not promote a recommendation into a fact merely because it is a common best practice.

Framework conventions such as `BaseDO`, soft delete, tenant columns, or audit fields are technical context, not business requirements.

## 4. Separate identity from labels

Names, codes, descriptions, and display labels can change. Identity should remain stable.

Do not use mutable display text as the only identity for durable business records unless the domain explicitly guarantees immutability.

## 5. Normalize before denormalizing

Use normalization to reveal where business facts truly belong.

Intentional duplication is acceptable when its purpose is explicit, for example:

- immutable historical value snapshot;
- historical relationship reference;
- performance-driven denormalization backed by a clear source of truth;
- integration boundary requirement;
- derived/cache value that can be recomputed.

Unexplained duplication is a design smell.

### Transitive redundancy matters

Do not only search for duplicate column names. Trace dependency chains.

If:

`production_record.product_version_id -> product_version.product_id`

then storing `production_record.product_id` as well means the product is transitively derivable.

Ask:

- What is the benefit of storing both?
- Can they disagree?
- Which value is authoritative?
- Can a database constraint guarantee consistency?
- Is the query benefit worth the additional invariant?

A design review that says "all redundancy is classified" must include this dependency walk.

## 6. Constraints are part of the model

Important domain rules should be represented where practical using:

- primary keys;
- foreign keys;
- unique constraints;
- not-null constraints;
- check constraints.

Do not rely exclusively on service-layer validation for invariants the database can safely enforce.

When multiple stored paths describe the same fact, explicitly test whether contradictory rows are possible.

## 7. Avoid generic structures too early

Be suspicious of:

- `type` + `value` tables used for unrelated concepts;
- universal key-value storage;
- JSON blobs replacing well-understood relationships;
- generic `status` fields whose legal transitions are undefined;
- one giant master table for unrelated business concepts.

Generic abstractions are justified only when the business abstraction is itself real and stable.

## 8. Many-to-many usually hides a business concept

When A and B have a many-to-many relationship, ask whether the relationship has its own:

- attributes;
- lifecycle;
- effective period;
- role;
- status;
- ordering;
- business rules.

If yes, model the association explicitly as a business concept rather than as an anonymous join table.

## 9. Deletion is domain behavior

Do not add soft delete by default.

Decide whether records should be:

- physically deletable;
- immutable once referenced;
- deactivated;
- archived;
- superseded;
- retained for audit or legal reasons.

The correct strategy depends on business semantics, not framework conventions.

## 10. Audit fields are not free

`created_at`, `updated_at`, `created_by`, `updated_by` can be useful, but they do not automatically provide a meaningful audit trail.

If the business requires reconstruction of past states, use a deliberate historical model rather than assuming timestamp columns are sufficient.

## 11. Business time is not recording time

A fact may occur on one date and be entered into the system later.

Always ask whether a historical relationship or value should be resolved at:

- the business-effective time; or
- the system recording time.

Late entry and backdated correction are mandatory stress cases when ownership, department, price, rate, version, or standard can change.

## 12. Keep unresolved decisions out of the final physical model

When two materially different representations satisfy the requirement, compare them before selecting one.

If the decision remains unresolved, mark affected physical elements as blocked or show parallel candidates.

Do not say "unit is undecided" and then quietly define `actual_work_hours DECIMAL(6,2)`.

Do not say "current-version semantics are undecided" and then quietly add `current_version_id` as if approved.

## 13. Design for known access patterns, not imagined scale

Indexes should map to known query predicates, joins, sorting, uniqueness, and workload characteristics.

Do not create broad speculative index sets before access patterns are known.

## 14. Prefer specific names

Names should communicate business meaning clearly.

Prefer `product_version` over `version_data`, `production_plan` over `biz_record`, and `standard_workhour` over `config_detail` when those are the real concepts.

## 15. Explain trade-offs

When multiple valid models exist, present the important alternatives and explain what each optimizes for. Do not conceal consequential choices behind implementation detail.
