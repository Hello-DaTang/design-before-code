# Design Before Code — Project Charter

This document is the project's anti-drift reference. Skills, integrations, benchmarks, and future roadmap changes should be checked against it before they are accepted.

## Mission

**Before AI writes code, make consequential business, UX, and data-model decisions visible, understandable, challengeable, and explicitly reviewable by humans.**

The project exists because coding agents can make good implementation progress while silently deciding product behavior, interaction flow, entity boundaries, historical semantics, and database structure. Those decisions should become reviewable artifacts before implementation makes them expensive to change.

## Primary user problem

A normal application developer or product owner may be able to judge whether a business flow feels correct, but may not be a senior DBA, domain architect, or UX specialist.

The project should help that person answer, before coding:

- What are the core business concepts and why do they exist?
- What does the user actually do and in what order?
- What data is stored, why is it stored, and what is derived?
- What changes over time, and what should history mean after it changes?
- Which decisions came from requirements and which came from the agent?
- Which important questions still require a human decision?
- Can a realistic scenario break the proposed design?

## Scope

### Primary scope

- Greenfield / 0→1 application design.
- Enterprise web systems, CRUD-heavy systems, and moderately complex business workflows as the initial proving ground.
- Early MVP review when the implementation is still cheap to reshape.
- Reusable Agent Skills that can run independently of a specific orchestration framework.

### Integration scope

OpenSpec may orchestrate these skills later, but OpenSpec is not the product's core identity. The skills should remain useful from Codex, Copilot, Claude Code, Cursor, Gemini, or other compatible agent environments.

## Non-goals for the current project

- Full legacy/brownfield reverse engineering.
- Automatic database migration/refactoring of large existing systems.
- Producing one canonical schema for every requirement.
- Replacing a professional DBA, UX designer, product owner, or domain expert.
- Maximizing document length, architecture sophistication, or table count.
- Turning every business system into DDD, event sourcing, CQRS, temporal tables, or another favored architecture.

## Product invariants

These principles must survive future revisions.

### 1. Human reviewability over agent convenience

The artifact should help a normal developer understand and challenge the design. A technically sophisticated answer that hides the consequential choice is a failure.

### 2. Design before implementation

The skills expose important decisions before implementation. They do not use generated code as the primary way to discover the design.

### 3. Decision provenance

Facts, inferences, recommendations, assumptions, and human decisions must remain distinguishable. A plausible best practice is not automatically a requirement.

### 4. No silent convergence

When two materially different models represent different business semantics, the agent must expose the choice instead of silently selecting its favorite representation.

### 5. Scenario-based validation

A design is not considered understood merely because an ERD looks normalized. Realistic event sequences and adversarial mutations should test whether the model still tells the correct business story.

### 6. Domain-general reasoning

Rules added from one benchmark must be generalized before entering a core skill. The project must not become a manufacturing-, ecommerce-, or framework-specific prompt.

### 7. Simplicity is a constraint

The skill should challenge both under-modeling and over-modeling. A new table, version layer, snapshot, history table, generic abstraction, or framework mechanism must justify its existence.

### 8. Framework-agnostic core

Existing code/framework conventions may inform a physical candidate, but should not redefine business truth. Integration-specific instructions belong in integrations or examples when possible.

### 9. Human gate for consequential ambiguity

If an unresolved choice can materially change identity, lifecycle, table boundaries, cardinality, historical semantics, uniqueness, canonical units, or user-visible behavior, implementation is not ready.

### 10. Regression evidence over intuition

Changes to a skill should be motivated by observed failures, generalized into a reusable principle, and protected by evals.

## Anti-overfitting rule

Before adding a new core rule because of one failed benchmark, ask:

1. Is the failure domain-specific, framework-specific, or generally applicable?
2. Can the rule be stated without mentioning the benchmark's product/table names?
3. Would the rule improve reasoning in at least one substantially different domain?
4. Does the new rule conflict with an existing valid business policy?
5. Can it be added as an eval without requiring one canonical schema?

If the rule only makes one benchmark produce the expected schema, it does not belong in the core skill.

## Evaluation philosophy

A benchmark passes because the agent exposes and reasons about consequential choices, not because it matches a golden table list.

Evaluate at least these dimensions:

- decision transparency;
- business meaning and lifecycle reasoning;
- redundancy and integrity reasoning;
- temporal/change semantics;
- scenario simulation quality;
- avoidance of premature physical decisions;
- avoidance of speculative architecture;
- human readability;
- correctness of database claims;
- ability to preserve unresolved decisions for human review.

Cross-domain evaluation is mandatory before declaring a major behavior stable.

## Iteration protocol

Use this loop:

```text
real or synthetic requirement
        ↓
baseline / current skill run
        ↓
observed failure
        ↓
generalize the failure
        ↓
change the smallest relevant rule/reference
        ↓
add regression eval
        ↓
rerun old evals
        ↓
run a different-domain benchmark
```

Do not keep rewriting the skill against one benchmark after its regression purpose has been satisfied.

## Current roadmap

1. Stabilize `data-model-design` across multiple domains.
2. Build `business-domain-design` for business concepts, events, rules, and domain boundaries before data modeling.
3. Build `ux-flow-design` for user journeys, information architecture, interaction states, failure/recovery paths, and low-fidelity flow review before UI implementation.
4. Build `design-readiness-review` to cross-check business, UX, and data artifacts and enforce the human gate.
5. Add OpenSpec orchestration after individual skills prove useful independently.

## Definition of success

Design Before Code is succeeding when a user can review an AI-generated design before coding and say things such as:

- "I understand why this entity/table exists."
- "I can see which rule came from the business and which one the AI suggested."
- "I know what happens to old data when this value changes."
- "I can see the alternatives before choosing one."
- "This user/data flow feels wrong; change it before implementation."
- "The agent found a decision we had not realized we needed to make."

The project is drifting if success becomes measured mainly by how complete the SQL, architecture, code generation, or framework integration looks.

## Change discipline

When a future session proposes a major direction change, compare it against this charter first. If the mission or product invariants themselves should change, update this document deliberately and explain why rather than allowing the change to happen implicitly through repeated prompt edits.
