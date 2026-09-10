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

## Current core skills

### `business-domain-design` v0.1

[`skills/business-domain-design/SKILL.md`](skills/business-domain-design/SKILL.md)

Turns rough requirements and meeting notes into a reviewable business model before UX, data, API, or code design.

It focuses on actors/goals, terminology, business concepts, lifecycles, states/events, rules, scenarios, decision provenance, and downstream constraints. It deliberately avoids ritual DDD and does not generate tables/pages/code.

### `ux-flow-design` v0.1

[`skills/ux-flow-design/SKILL.md`](skills/ux-flow-design/SKILL.md)

Turns an understood business model into reviewable user task flows before Vue/React/Figma implementation.

It focuses on actor jobs, task-first information architecture, primary journeys, page/surface responsibilities, exception/recovery states, backdated/historical entry behavior, low-fidelity wireframes, and downstream API/data implications created by UX promises.

It deliberately avoids visual-branding/design-system work and does not generate frontend code.

### `data-model-design` v0.2.1

[`skills/data-model-design/SKILL.md`](skills/data-model-design/SKILL.md)

Designs and reviews a data model through decision provenance, conceptual/logical/physical candidates, temporal review, scenario simulation, design review, and a human gate.

Current strengths include transitive redundancy review, declarative-integrity reasoning, business-effective vs recording time, historical relationship vs value snapshot semantics, mutable-fact policy, canonical units, and physical-model decision locks.

### `design-readiness-review` v0.1

[`skills/design-readiness-review/SKILL.md`](skills/design-readiness-review/SKILL.md)

Cross-reviews the business, UX, and data artifacts before implementation.

It does not summarize them independently. It checks:

- decision drift across artifacts;
- concept/terminology alignment;
- lifecycle/state/event consistency;
- primary task → business rule → UX journey → data/API traceability;
- UX promises unsupported by data/lifecycle;
- data constraints unsupported by business semantics;
- historical behavior consistency;
- cross-artifact scenario correctness;
- BLOCKER / MAJOR / MINOR findings;
- READY / NEEDS DECISION / NOT READY implementation gate.

## First complete workflow

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

The four Skills remain independently usable. OpenSpec is intended to orchestrate the artifacts later, not define the core product.

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
- [`evals/design-readiness-review.md`](evals/design-readiness-review.md)

## Repository structure

```text
design-before-code/
├── docs/
│   ├── PROJECT-CHARTER.md
│   ├── EVALUATION-PROTOCOL.md
│   └── ROADMAP.md
├── skills/
│   ├── business-domain-design/
│   ├── ux-flow-design/
│   ├── data-model-design/
│   └── design-readiness-review/
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

The readiness reviewer is a **gate and router**, not a universal fourth designer. If it finds a defect, the issue should be routed back to the owning layer: business semantics, UX flow, or data model.

## Status

Early experimental project — **4 of 4 core Skills now have a minimum usable version**.

Current priority: validate the complete chain with low-cost cross-artifact evals, then build OpenSpec orchestration so implementation stays blocked until readiness review and human approval are complete.
