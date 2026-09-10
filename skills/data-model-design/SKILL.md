# data-model-design

Design and review an application data model before implementation. Optimize for human reviewability first, database correctness second, and implementation convenience third.

**Current behavior target: v0.2.2.**

## When to use

Use this skill for greenfield features and early MVPs when the data model is not yet trusted or understood.

Do not use it as a full legacy-database reverse-engineering or migration framework.

## Non-negotiable rules

1. **Do not jump directly from requirements to SQL, ORM entities, migrations, or repository code.**
2. **Do not silently turn recommendations, framework conventions, or plausible assumptions into business facts.**
3. **Do not finalize a physical representation while a material business decision that changes that representation is unresolved.**
4. **Do not accept duplicated or transitively derivable facts without an explicit redundancy review.**
5. **Do not claim historical correctness without distinguishing business-effective time from system recording time.**
6. **Do not claim a duplicated fact cannot be protected by the database until declarative integrity options in the target database have been considered.**
7. **Do not treat input/display units as an approved canonical storage unit when calculations or precision depend on that choice.**
8. **Do not equate a coherent data model with approval to implement.** A data-model artifact can be ready for downstream technical design while cross-artifact readiness and human approval are still pending.

Before implementation, make the design visible in this sequence:

1. Context and decision provenance
2. Conceptual model
3. Logical model
4. Physical model candidates
5. Temporal review
6. Scenario simulation
7. Design review
8. Human review gate

If information is missing, list assumptions and unknowns explicitly. Do not silently invent consequential business rules.

## Required output

### 1. Context and decision provenance

Summarize:

- business goal;
- actors involved;
- important terminology;
- known rules;
- unknowns and assumptions that materially affect the model.

For consequential statements, distinguish these categories:

- **FACT** — explicitly stated by the business/request.
- **INFERENCE** — logically derived from stated facts.
- **RECOMMENDATION** — a design choice proposed by the agent.
- **ASSUMPTION** — a provisional choice made only to continue analysis.
- **DECISION REQUIRED** — a human/business choice whose answer materially changes identity, table boundaries, cardinality, historical semantics, uniqueness, lifecycle, or canonical data representation.

A recommendation must never be presented as if the business already approved it.

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

Also challenge missing entities. If two facts have different lifecycles — for example a production plan exists before an actual production record — ask whether they are truly one concept or separate concepts.

### 4. Physical model candidates

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

#### Mandatory dependency and redundancy walk

For every stored foreign key or business attribute that may be derivable through another stored relationship, ask:

1. Can this value be derived transitively from another stored value?
2. If both are stored, can they contradict each other?
3. What is the authoritative source of truth?
4. What concrete performance, historical, integration, partitioning, uniqueness, or integrity reason justifies duplication?
5. What declarative mechanisms in the target database could enforce consistency if both remain?

Before saying "the database cannot enforce this," consider applicable mechanisms such as composite foreign keys backed by composite UNIQUE keys, CHECK constraints, generated columns, exclusion/partial constraints where supported, or a different key design. State target-database limitations precisely rather than generically.

Example pattern to challenge:

`production_record.product_id` plus `production_record.product_version_id` when `product_version_id -> product_id` already determines the product.

If both are kept, classify the duplication, identify the source of truth, and show how contradictions are prevented. Otherwise prefer the non-redundant representation.

Redundancy review is not automatic redundancy rejection: a duplicate may be justified if its invariant is explicit and enforceable enough for the risk it introduces.

#### Canonical units and quantities

When two values participate in calculations, comparisons, constraints, or aggregation, distinguish:

- the unit users enter;
- the unit users see;
- the canonical unit stored or normalized for computation.

If choosing seconds vs hours, cents vs decimal currency, grams vs kilograms, or another canonical representation affects type, precision, rounding, constraints, or interoperability, treat it as **DECISION REQUIRED** unless already specified. Do not finalize the physical field/type merely because the requirement examples use a particular display/input unit.

#### Decision lock

If a **DECISION REQUIRED** item materially changes the physical schema, do not silently choose one representation in the "final" model.

Instead do one of these:

- show Option A / Option B physical candidates;
- mark the affected table/column as **BLOCKED BY Dn**;
- defer that physical detail until the human decision is made.

Do not write a concrete column such as `actual_work_hours DECIMAL(...)` while simultaneously saying the canonical storage unit is still undecided.

SQL may be included here if useful, but SQL is not the primary artifact.

### 5. Temporal review

For every value or relationship that can change over time, determine the intended historical semantics.

First distinguish:

- **Business-effective time** — when the real-world fact was true or the business event occurred.
- **System recording time** — when the row was inserted or updated in the system.

Do not assume these are the same. Late entry, backdated correction, imports, delayed approval, and retroactive correction can make them different.

For each mutable fact, also distinguish **why** it changes:

- **prospective business change** — the world changes from a stated effective time forward;
- **retroactive correction** — the system learns that a past fact was recorded incorrectly;
- **current-value reinterpretation** — the business intentionally wants old reports/calculations to use the latest current definition;
- **original-applied preservation** — old events must keep the exact value/definition actually applied at the time.

These semantics are different. Do not use effective-dated history as a generic answer to every mutable fact, and do not assume historical stability is always desired.

Then classify the storage/history strategy precisely:

- **Current identity/reference** — keep stable identity and intentionally resolve current mutable attributes.
- **Historical relationship reference** — preserve which related entity was associated with the event, without freezing all attributes of that entity.
- **Value snapshot** — copy the exact value applied at business-effective time.
- **Effective-dated history** — store versions/relationships with validity periods so the system can reconstruct what was effective at a past business time.
- **Recorded-change/audit history** — preserve when the system learned or changed a fact when that distinction matters.

If both "what was effective then?" and "what did the system believe then?" matter, flag the need to model both valid time and transaction/recording time rather than pretending one timestamp answers both questions.

Be precise with terminology: storing `dept_id` may preserve the department relationship, but it is not a full department-value snapshot if the department name is still read from mutable master data.

Explicitly review names, prices, classifications, versions, standards, ownership, organizational relationships, and other mutable business facts that could affect historical truth.

Never duplicate a mutable field merely "for convenience" without saying whether the duplicate is a value snapshot, historical relationship reference, cache/denormalization, integration copy, or mistake.

Read `references/temporal-data.md` when temporal behavior is consequential.

### 6. Scenario simulation

Run at least one realistic end-to-end example through the model.

Show:

- the business event sequence;
- business-effective dates/times when relevant;
- system recording dates/times when relevant;
- which records/entities are created or changed;
- what a user would expect to see later;
- what happens after relevant current data changes;
- whether the proposed model preserves the intended historical truth.

For models involving mutable ownership, organizational assignment, pricing, standards, versions, or backdated business data, include at least one adversarial mutation such as:

- business event occurs, related master data changes, then the event is entered late;
- backdated correction after a version/department/rate change;
- prospective rate/standard change followed by a historical query;
- master-data rename or reclassification;
- deactivation or organizational restructuring;
- intentional "recalculate history using latest value" policy.

If the scenario reveals a contradiction, revise the model before recommending implementation.

Read `references/scenario-simulation.md` for the procedure.

### 7. Design review

Before declaring the design ready, run the checklist in `references/review-checklist.md`.

Call out specifically:

- redundant fields or tables;
- **transitively derivable fields** and duplicated business facts;
- contradictory foreign-key paths and whether declarative constraints can prevent them;
- ambiguous ownership;
- many-to-many relationships without an explicit associative concept;
- missing uniqueness constraints;
- history that could be rewritten accidentally or intentionally;
- confusion between prospective change, retroactive correction, and latest-value reinterpretation;
- confusion between business-effective time and recording time;
- reference identity mislabeled as a full snapshot;
- unresolved canonical units already embedded in physical fields;
- unresolved decisions already embedded in the physical schema;
- tables created only to satisfy UI layout or code structure;
- speculative extensibility;
- overuse of soft delete, generic status fields, JSON blobs, audit columns, or version tables.

### 8. Human review gate

End with a compact decision section:

- **Ready for downstream technical design** — the data model is coherent enough to feed later technical design/review. This is **not** human approval and **not** permission to begin implementation when a broader workflow still requires cross-artifact readiness or explicit approval.
- **Needs human decision** — list each unresolved choice and its consequences.
- **Not ready** — explain what information is missing.

For each unresolved item, also name the physical model elements that remain blocked by it.

Never treat this skill's own readiness result as approval on the user's behalf. In an orchestrated workflow, preserve any later `design-readiness` and human-approval gates.

## Design principles

- Model business reality before storage structure.
- Prefer explicit business meaning over generic abstractions.
- A table should represent a durable concept, relationship, event, or justified technical necessity.
- Data integrity should not depend only on application code when the database can enforce the rule safely.
- Normalize to a clear baseline before intentional denormalization.
- Trace transitive dependencies, not only duplicate column names.
- Explore declarative integrity mechanisms before declaring an invariant unenforceable in the database.
- Historical truth must be designed around business-effective time and change semantics, not merely row timestamps.
- A stable foreign-key identity is not automatically a value snapshot.
- Input/display units are not automatically canonical storage units.
- Avoid speculative flexibility. Design for known variation and make unknown variation visible.
- Keep unresolved decisions unresolved in the physical model rather than quietly choosing for the user.
- Optimize the explanation so a normal application developer can review it without being a DBA.

Read `references/modeling-principles.md` for deeper guidance.
