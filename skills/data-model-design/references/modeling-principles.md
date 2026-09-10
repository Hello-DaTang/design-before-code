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

## 3. Separate identity from labels

Names, codes, descriptions, and display labels can change. Identity should remain stable.

Do not use mutable display text as the only identity for durable business records unless the domain explicitly guarantees immutability.

## 4. Normalize before denormalizing

Use normalization to reveal where business facts truly belong.

Intentional duplication is acceptable when its purpose is explicit, for example:

- immutable historical snapshot;
- performance-driven denormalization backed by a clear source of truth;
- integration boundary requirement;
- derived/cache value that can be recomputed.

Unexplained duplication is a design smell.

## 5. Constraints are part of the model

Important domain rules should be represented where practical using:

- primary keys;
- foreign keys;
- unique constraints;
- not-null constraints;
- check constraints.

Do not rely exclusively on service-layer validation for invariants the database can safely enforce.

## 6. Avoid generic structures too early

Be suspicious of:

- `type` + `value` tables used for unrelated concepts;
- universal key-value storage;
- JSON blobs replacing well-understood relationships;
- generic `status` fields whose legal transitions are undefined;
- one giant master table for unrelated business concepts.

Generic abstractions are justified only when the business abstraction is itself real and stable.

## 7. Many-to-many usually hides a business concept

When A and B have a many-to-many relationship, ask whether the relationship has its own:

- attributes;
- lifecycle;
- effective period;
- role;
- status;
- ordering;
- business rules.

If yes, model the association explicitly as a business concept rather than as an anonymous join table.

## 8. Deletion is domain behavior

Do not add soft delete by default.

Decide whether records should be:

- physically deletable;
- immutable once referenced;
- deactivated;
- archived;
- superseded;
- retained for audit or legal reasons.

The correct strategy depends on business semantics, not framework conventions.

## 9. Audit fields are not free

`created_at`, `updated_at`, `created_by`, `updated_by` can be useful, but they do not automatically provide a meaningful audit trail.

If the business requires reconstruction of past states, use a deliberate historical model rather than assuming timestamp columns are sufficient.

## 10. Design for known access patterns, not imagined scale

Indexes should map to known query predicates, joins, sorting, uniqueness, and workload characteristics.

Do not create broad speculative index sets before access patterns are known.

## 11. Prefer specific names

Names should communicate business meaning clearly.

Prefer `product_version` over `version_data`, `production_plan` over `biz_record`, and `standard_workhour` over `config_detail` when those are the real concepts.

## 12. Explain trade-offs

When multiple valid models exist, present the important alternatives and explain what each optimizes for. Do not conceal consequential choices behind implementation detail.
