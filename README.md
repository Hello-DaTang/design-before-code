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

It focuses on actors/goals, terminology, business concepts, lifecycles, states/events, rules, scenarios, decision provenance, and downstream constraints. It deliberately avoids ritual DDD and does not generate tables/pages/code.

### `ux-flow-design` v0.1

[`skills/ux-flow-design/SKILL.md`](skills/ux-flow-design/SKILL.md)

Turns an understood business model into reviewable user task flows before Vue/React/Figma implementation.

It focuses on:

- actor jobs and task priority;
- information architecture driven by user goals rather than backend entities;
- primary journeys and decision points;
- page/surface responsibilities;
- page vs dialog/drawer choices;
- empty/loading/error/permission/conflict/recovery states;
- historical/backdated entry behavior;
- low-fidelity wireframes;
- downstream API/data implications created by UX promises;
- human review gate before frontend implementation.

It deliberately avoids visual-branding/design-system work and does not generate frontend code.

### `data-model-design` v0.2.1

[`skills/data-model-design/SKILL.md`](skills/data-model-design/SKILL.md)

Designs and reviews a data model through decision provenance, conceptual/logical/physical candidates, temporal review, scenario simulation, design review, and a human gate.

Current strengths include transitive redundancy review, declarative-integrity reasoning, business-effective vs recording time, historical relationship vs value snapshot semantics, mutable-fact policy, canonical units, and physical-model decision locks.

## Planned skill

### `design-readiness-review`

Cross-checks business, UX, and data artifacts and answers:

> Do these three views describe the same system, and is it safe to start implementation?

It will detect contradictions, run a cross-artifact scenario, surface blocked decisions, and produce a READY / NEEDS DECISION / NOT READY implementation gate.

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

We do not rerun expensive full A/B experiments for every edit.

Evaluation is tiered:

1. **Unit Eval** — one compact behavior test.
2. **Regression Sample** — new eval + 2–4 older cases.
3. **Domain Benchmark** — realistic cross-domain requirement at milestones.
4. **A/B Benchmark** — rare; used for first baselines, major rewrites, or major release evidence.

See [`docs/EVALUATION-PROTOCOL.md`](docs/EVALUATION-PROTOCOL.md).

Current evals:

- [`evals/business-domain-design.md`](evals/business-domain-design.md)
- [`evals/ux-flow-design.md`](evals/ux-flow-design.md)
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
│   ├── ux-flow-design/
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

Early experimental project — **3 of 4 core Skills now have a minimum usable version**.

Current priority: build `design-readiness-review` v0.1 and complete the first end-to-end pre-implementation design loop before investing heavily in OpenSpec orchestration or deeper single-skill tuning.
