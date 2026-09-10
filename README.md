# Design Before Code

> Before AI writes code, make business, UX, and data-model decisions reviewable by humans.

Design Before Code is a collection of Agent Skills for making important product and data-design decisions explicit **before implementation starts**.

The project is **greenfield-first**. It is designed for 0→1 application development where requirements may exist as meeting notes, domain explanations, rough workflows, or product ideas, but the database and UX should not silently emerge during coding.

It can also be used for **early MVP review**, where a small amount of code already exists and the goal is to compare the current design with an intended target design. It is not currently intended to be a full legacy-code reverse-engineering or large-scale brownfield refactoring framework.

The project's long-term mission and anti-drift rules are defined in [`docs/PROJECT-CHARTER.md`](docs/PROJECT-CHARTER.md). Major skill or roadmap changes should be checked against that charter rather than emerging implicitly from repeated benchmark tuning.

## Why

AI coding agents are very good at implementation, but they can also make important design decisions implicitly while coding:

- creating tables as implementation needs appear;
- introducing redundant fields without an explicit historical-data strategy;
- shaping user flows around code structure instead of user intent;
- adding architecture or schema complexity that the human reviewer never approved;
- treating reasonable recommendations as if they were confirmed requirements;
- using current master data to reconstruct historical events that happened earlier;
- finalizing physical schema choices while the business decision is still unresolved;
- discovering product decisions only after the MVP already exists.

This project moves those decisions earlier and turns them into reviewable artifacts.

## Current scope — data-model-design v0.2.1

The first implemented skill is:

- [`data-model-design`](skills/data-model-design/SKILL.md) — design and review a data model through conceptual, logical, physical, temporal, scenario-validation, and human-gate stages before implementation.

v0.2 introduced:

- decision provenance: FACT / INFERENCE / RECOMMENDATION / ASSUMPTION / DECISION REQUIRED;
- transitive and derived redundancy review, not only duplicate-column review;
- business-effective time vs system recording time;
- historical relationship references vs full value snapshots;
- adversarial late-entry/backdated scenario simulation;
- physical-model decision locks when material business semantics are unresolved.

v0.2.1 refines that behavior by adding:

- declarative-integrity review before claiming intentional redundancy cannot be database-enforced (for example composite FK + composite UNIQUE patterns where supported);
- explicit distinction between prospective business change, retroactive correction, original-applied preservation, and intentional latest-value reinterpretation;
- valid/effective time vs recording/transaction-time reasoning when both questions matter;
- canonical unit review: input/display units are not automatically the approved storage/computation unit.

Planned skills:

- `business-domain-design`
- `ux-flow-design`
- `design-readiness-review`
- OpenSpec workflow integration

## Core principle

**Do not jump from requirements directly to SQL.**

A data model should become understandable in this order:

1. Context and decision provenance — what is fact, inference, recommendation, assumption, or a human decision?
2. Conceptual model — what exists in the real world?
3. Logical model — what identities, relationships, rules, and lifecycles exist?
4. Physical model candidates — how could the approved semantics be represented in the target database?
5. Temporal review — what changes, why does it change, what must history mean, and at what business-effective/system time?
6. Scenario simulation — does the model survive realistic mutations, late entry, correction, and policy changes?
7. Design review — are redundancy, integrity, lifecycle, units, and unresolved decisions visible?
8. Human review gate — unresolved consequential choices remain blocked before coding.

## Repository structure

```text
design-before-code/
├── docs/
│   └── PROJECT-CHARTER.md
├── skills/
│   └── data-model-design/
│       ├── SKILL.md
│       └── references/
├── integrations/
│   └── openspec/
├── examples/
│   └── manufacturing/
├── evals/
├── ATTRIBUTION.md
├── CHANGELOG.md
├── LICENSE
└── README.md
```

## Intended workflow

```text
meeting notes / requirements
          ↓
   business understanding
          ↓
      UX flow design
          ↓
     data model design
          ↓
   scenario simulation
          ↓
      human review
          ↓
        coding
```

Design Before Code does not try to replace OpenSpec, Spec Kit, BMAD, or other specification systems. The intended long-term direction is to provide reusable design intelligence that can be called independently or orchestrated by tools such as OpenSpec.

## Evaluation philosophy

The project does not evaluate a skill by checking whether it produces one canonical schema.

Instead, evaluations ask whether the agent:

- exposes consequential ambiguity before implementation;
- distinguishes requirements from its own recommendations;
- detects redundant and contradictory representations;
- explores enforceable integrity rather than making vague database-capability claims;
- preserves or intentionally reinterprets historical business meaning according to explicit policy;
- avoids premature physical decisions;
- produces artifacts a normal application developer can challenge and approve.

Cross-domain evaluation is required to prevent benchmark overfitting. A failure found in one domain should become a generalized rule only when it transfers beyond that single case.

See [`evals/data-model-design.md`](evals/data-model-design.md) for the current regression suite and [`docs/PROJECT-CHARTER.md`](docs/PROJECT-CHARTER.md) for the anti-drift rules.

## Status

Early experimental **data-model-design v0.2.1**.

The current goal is to stabilize data-model reasoning across substantially different domains before expanding into business-domain and UX skills.
