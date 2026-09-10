# Design Before Code

> Before AI writes code, make business, UX, and data-model decisions reviewable by humans.

Design Before Code is a collection of Agent Skills for making important product and data-design decisions explicit **before implementation starts**.

The project is **greenfield-first**. It is designed for 0→1 application development where requirements may exist as meeting notes, domain explanations, rough workflows, or product ideas, but the database and UX should not silently emerge during coding.

It can also be used for **early MVP review**, where a small amount of code already exists and the goal is to compare the current design with an intended target design. It is not currently intended to be a full legacy-code reverse-engineering or large-scale brownfield refactoring framework.

## Why

AI coding agents are very good at implementation, but they can also make important design decisions implicitly while coding:

- creating tables as implementation needs appear;
- introducing redundant fields without an explicit historical-data strategy;
- shaping user flows around code structure instead of user intent;
- adding architecture or schema complexity that the human reviewer never approved;
- discovering product decisions only after the MVP already exists.

This project moves those decisions earlier and turns them into reviewable artifacts.

## v0.1 scope

The first implemented skill is:

- [`data-model-design`](skills/data-model-design/SKILL.md) — design and review a data model through conceptual, logical, physical, temporal, and scenario-validation stages before implementation.

Planned skills:

- `business-domain-design`
- `ux-flow-design`
- `design-readiness-review`
- OpenSpec workflow integration

## Core principle

**Do not jump from requirements directly to SQL.**

A data model should become understandable in this order:

1. Conceptual model — what exists in the real world?
2. Logical model — what identities, relationships, rules, and lifecycles exist?
3. Physical model — how should the model be represented in the target database?
4. Temporal review — what must remain historically true when current data changes?
5. Scenario simulation — does the model survive a realistic business example?
6. Human review gate — unresolved assumptions and consequential design choices remain visible before coding.

## Repository structure

```text
design-before-code/
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

## Status

Early experimental v0.1. The current goal is to validate the workflow on real enterprise CRUD and moderately complex business systems before expanding the skill set.
