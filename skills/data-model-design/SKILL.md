# data-model-design

Design and review an application data model before implementation. Optimize for human reviewability first, database correctness second, and implementation convenience third.

## When to use

Use this skill for greenfield features and early MVPs when the data model is not yet trusted or understood.

Do not use it as a full legacy-database reverse-engineering or migration framework.

## Non-negotiable rule

**Do not jump directly from requirements to SQL, ORM entities, migrations, or repository code.**

Before implementation, make the design visible in the following sequence:

1. Conceptual model
2. Logical model
3. Physical model
4. Temporal review
5. Scenario simulation
6. Human review gate

If information is missing, list assumptions and unknowns explicitly. Do not silently invent consequential business rules.

## Required output

### 1. Context and assumptions

Summarize:

- business goal;
- actors involved;
- important terminology;
- known rules;
- unknowns and assumptions that materially affect the model.

Separate facts from assumptions.

### 2. Conceptual model

Describe the real-world business concepts before database terminology.

For each concept include:

- what it represents in the real world;
- its identity, if it has one;
- important lifecycle or state changes;
- relationships to other concepts.

Prefer a small Mermaid relationship diagram or readable text diagram.

Do not discuss column types or indexes here.

### 3. Logical model

Translate business concepts into entities and relationships without committing to database-specific details.

For every proposed entity answer:

- **Why this exists**
- real-world meaning
- why it needs its own identity
- why it should be separate rather than embedded or duplicated elsewhere
- lifecycle
- cardinality and ownership relationships
- invariants and validation rules
- deletion semantics

Challenge unnecessary entities. Do not create a table merely because a page, DTO, form, or API response contains a group of fields.

### 4. Physical model

Only after the conceptual and logical models are understandable, propose a concrete relational representation.

Include:

- tables and columns;
- primary keys;
- foreign keys;
- unique constraints;
- nullability;
- important check constraints;
- indexes justified by known access patterns;
- audit fields only when justified;
- soft delete only when justified;
- optimistic locking only when justified.

Normalize first. Deliberate denormalization requires an explicit reason.

SQL may be included here if useful, but SQL is not the primary artifact.

### 5. Temporal review

For every value that can change over time, determine which historical strategy applies:

- **Current reference** — historical records intentionally show the latest current value.
- **Snapshot** — historical records preserve the value as it was when the event occurred.
- **Effective-dated history** — multiple versions are stored with validity periods.

Explicitly review names, prices, classifications, versions, standards, ownership, organizational relationships, and other mutable business facts that could affect historical truth.

Never duplicate a mutable field merely "for convenience" without saying whether the duplicate is a snapshot, cache, denormalization, or mistake.

Read `references/temporal-data.md` when temporal behavior is consequential.

### 6. Scenario simulation

Run at least one realistic end-to-end example through the model.

Show:

- the business event sequence;
- which records/entities are created or changed;
- what a user would expect to see later;
- what happens after relevant current data changes;
- whether the proposed model preserves the intended historical truth.

If the scenario reveals a contradiction, revise the model before recommending implementation.

Read `references/scenario-simulation.md` for the procedure.

### 7. Design review

Before declaring the design ready, run the checklist in `references/review-checklist.md`.

Call out specifically:

- redundant fields or tables;
- duplicated business facts;
- ambiguous ownership;
- many-to-many relationships without an explicit associative concept;
- missing uniqueness constraints;
- history that could be rewritten accidentally;
- tables created only to satisfy UI layout or code structure;
- speculative extensibility;
- overuse of soft delete, generic status fields, JSON blobs, audit columns, or version tables.

### 8. Human review gate

End with a compact decision section:

- **Ready to implement** — only if no material ambiguity remains.
- **Needs human decision** — list each unresolved choice and its consequences.
- **Not ready** — explain what information is missing.

Do not self-approve unresolved product or business decisions.

## Design principles

- Model business reality before storage structure.
- Prefer explicit business meaning over generic abstractions.
- A table should represent a durable concept, relationship, event, or justified technical necessity.
- Data integrity should not depend only on application code when the database can enforce the rule safely.
- Normalize to a clear baseline before intentional denormalization.
- Historical truth must be designed, not discovered after production data exists.
- Avoid speculative flexibility. Design for known variation and make unknown variation visible.
- Optimize the explanation so a normal application developer can review it without being a DBA.

Read `references/modeling-principles.md` for deeper guidance.
