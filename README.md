# Design Before Code

> Before AI writes code, make business, UX, and data-model decisions reviewable by humans.

Design Before Code is a collection of Agent Skills for making consequential product and design decisions explicit **before implementation starts**.

The project is **greenfield-first**. It is designed for 0→1 application development where requirements may exist as meeting notes, domain explanations, rough workflows, or product ideas, but the business model, page flow, and database should not silently emerge during coding.

It can also support **early MVP review** while the implementation is still cheap to reshape. It is not currently intended to be a full legacy-code reverse-engineering or large-scale brownfield refactoring framework.

The project's long-term mission and anti-drift rules live in [`docs/PROJECT-CHARTER.md`](docs/PROJECT-CHARTER.md). The development sequence lives in [`docs/ROADMAP.md`](docs/ROADMAP.md), and evaluation cost/discipline is defined in [`docs/EVALUATION-PROTOCOL.md`](docs/EVALUATION-PROTOCOL.md).

## Why

AI coding agents can make good implementation progress while silently deciding:

- what the real business concepts are;
- whether two concepts with different lifecycles should be merged;
- what the user must click and in what order;
- what becomes a table or redundant field;
- how history behaves after prices, versions, departments, standards, or policies change;
- which recommendations are treated as if stakeholders already approved them.

Design Before Code moves those decisions earlier and turns them into artifacts a normal application developer or product owner can understand, challenge, and approve.

## Current skills

### `business-domain-design` v0.1

[`skills/business-domain-design/SKILL.md`](skills/business-domain-design/SKILL.md)

Turns rough requirements and meeting notes into a reviewable business model before UX, data, API, or code design.

It focuses on:

- actors and goals;
- terminology;
- business concepts and responsibilities;
- lifecycles, states, and events;
- rules and invariants;
- normal and exceptional scenarios;
- FACT / INFERENCE / RECOMMENDATION / ASSUMPTION / DECISION REQUIRED;
- downstream constraints that UX and data design must respect.

It deliberately avoids ritual DDD and does not generate tables/pages/code.

### `data-model-design` v0.2.1

[`skills/data-model-design/SKILL.md`](skills/data-model-design/SKILL.md)

Designs and reviews a data model through:

1. decision provenance;
2. conceptual model;
3. logical model;
4. physical model candidates;
5. temporal review;
6. scenario simulation;
7. design review;
8. human review gate.

Current strengths include transitive redundancy review, declarative-integrity reasoning, business-effective vs recording time, historical relationship vs value snapshot semantics, mutable-fact policy, canonical units, and physical-model decision locks.

## Planned skills

### `ux-flow-design`

Task-first UX design before Vue/React/Figma implementation:

- user goals;
- task/journey flow;
- information architecture;
- primary actions;
- normal + failure/recovery paths;
- empty/loading/error/permission/conflict states;
- low-fidelity wireframes;
- unresolved interaction decisions.

### `design-readiness-review`

Cross-checks business, UX, and data artifacts and answers:

> Do these three views describe the same system, and is it safe to start implementation?

It will produce a READY / NEEDS DECISION / NOT READY gate.

### OpenSpec integration

OpenSpec is intended to orchestrate the artifacts later, not define the core product. Each Skill should remain independently usable in compatible agent environments.

## Intended first complete workflow

```text
meeting notes / requirements
          ↓
business-domain-design
          ↓
    human review
          ↓
    ux-flow-design
          ↓
    human review
          ↓
   data-model-design
          ↓
    human review
          ↓
design-readiness-review
          ↓
   final human gate
          ↓
 OpenSpec / implementation
```

## Evaluation

We no longer rerun expensive full A/B experiments for every edit.

Evaluation is tiered:

1. **Unit Eval** — one compact behavior test.
2. **Regression Sample** — new eval + 2–4 older cases.
3. **Domain Benchmark** — realistic cross-domain requirement at milestones.
4. **A/B Benchmark** — rare; used for first baselines, major rewrites, or major release evidence.

See [`docs/EVALUATION-PROTOCOL.md`](docs/EVALUATION-PROTOCOL.md).

Current evals:

- [`evals/business-domain-design.md`](evals/business-domain-design.md)
- [`evals/data-model-design.md`](evals/data-model-design.md)

## Repository structure

```text
design-before-code/
├── docs/
│   ├── PROJECT-CHARTER.md
│   ├── EVALUATION-PROTOCOL.md
│   └── ROADMAP.md
├── skills/
│   ├── business-domain-design/
│   │   ├── SKILL.md
│   │   └── references/
│   └── data-model-design/
│       ├── SKILL.md
│       └── references/
├── integrations/
│   └── openspec/
├── examples/
├── evals/
├── ATTRIBUTION.md
├── CHANGELOG.md
├── LICENSE
└── README.md
```

## Project rule

A failure found in one benchmark may enter a core Skill only after it is generalized beyond that specific domain. We optimize for reusable reasoning, not benchmark-specific answers.

## Status

Early experimental project.

Current priority: build the complete pre-implementation design triangle instead of endlessly polishing one database skill.
