# Design Before Code

> Before AI writes code, make business, UX, and data-model decisions reviewable by humans.

Design Before Code is a collection of Agent Skills for making consequential product and design decisions explicit **before implementation starts**.

The project is **greenfield-first**. It is designed for 0→1 application development where requirements may exist as meeting notes, domain explanations, rough workflows, or product ideas, but the business model, page flow, and database should not silently emerge during coding.

It can also support **early MVP review** while the implementation is still cheap to reshape. It is not currently intended to be a full legacy-code reverse-engineering or large-scale brownfield refactoring framework.

The project's long-term mission and anti-drift rules live in [`docs/PROJECT-CHARTER.md`](docs/PROJECT-CHARTER.md). The development sequence lives in [`docs/ROADMAP.md`](docs/ROADMAP.md), evaluation discipline is defined in [`docs/EVALUATION-PROTOCOL.md`](docs/EVALUATION-PROTOCOL.md), and the upstream-method synthesis is documented in [`docs/METHOD-SYNTHESIS.md`](docs/METHOD-SYNTHESIS.md).

## Why

AI coding agents can make good implementation progress while silently deciding:

- what the real business concepts are;
- whether two concepts with different lifecycles should be merged;
- what the user must click and in what order;
- what becomes a table or redundant field;
- how history behaves after prices, versions, departments, standards, or policies change;
- which recommendations are treated as if stakeholders already approved them.

Design Before Code moves those decisions earlier and turns them into artifacts a normal application developer or product owner can understand, challenge, and approve.

## Method synthesis, not prompt copying

Design Before Code is intentionally **not** a closed-door methodology and **not** a collage of copied prompts.

Its core Skills synthesize proven mechanisms from projects such as:

- Superpowers — collaborative design-before-implementation and explicit approval gates;
- BMad Method — complexity-sensitive planning, behavioral UX artifacts, state coverage, and readiness review;
- GitHub Spec Kit — staged artifacts, clarification/gates, explicit data-model planning, and validation scenarios;
- DDD Agent Skill — DDD-worthiness gate, Event Storming, ubiquitous language, and optional domain-boundary discovery;
- softaworks `database-schema-designer` — normalization, constraints, access-pattern-driven indexes, and deliberate denormalization.

The detailed mechanism → adaptation map, including what is intentionally excluded, lives in [`docs/METHOD-SYNTHESIS.md`](docs/METHOD-SYNTHESIS.md). Source/license acknowledgements live in [`ATTRIBUTION.md`](ATTRIBUTION.md).

## Current core skills

### `business-domain-design` v0.2

[`skills/business-domain-design/SKILL.md`](skills/business-domain-design/SKILL.md)

Turns rough requirements and meeting notes into a reviewable business model before UX, data, API, or code design.

It includes a **Plain CRUD / Light Domain Modeling / Deep Domain Discovery** gate, optional Event Storming-lite, ubiquitous-language clarification, lifecycle/event/rule modeling, and explicit downstream constraints without forcing tactical DDD.

### `ux-flow-design` v0.2

[`skills/ux-flow-design/SKILL.md`](skills/ux-flow-design/SKILL.md)

Turns an understood business model into a behavioral UX contract before Vue/React/Figma implementation.

It focuses on named-protagonist journeys, task-first information architecture, surface responsibilities, state/recovery coverage, low-fidelity wireframes, accessibility floor, surface closure, and downstream API/data implications created by UX promises.

It deliberately separates **how it works** from visual-brand/design-system work.

### `data-model-design` v0.2.1

[`skills/data-model-design/SKILL.md`](skills/data-model-design/SKILL.md)

Designs and reviews a data model through decision provenance, conceptual/logical/physical candidates, temporal review, scenario simulation, design review, and a human gate.

It synthesizes explicit data-model artifact discipline with normalization/constraint/access-pattern reasoning, then adds this project's temporal-business-time, transitive-redundancy, declarative-integrity, canonical-unit, and human-decision-lock behavior.

### `design-readiness-review` v0.2

[`skills/design-readiness-review/SKILL.md`](skills/design-readiness-review/SKILL.md)

Cross-reviews the business, UX, and data artifacts before implementation.

It applies coverage lenses, decision-ledger reconciliation, lifecycle/state/event consistency, task-to-capability traceability, cross-artifact scenario walkthroughs, project-constraint re-checks, next-owner routing, and an explicit `READY_FOR_HUMAN_APPROVAL / NEEDS_DECISION / NOT_READY` gate.

The agent cannot approve implementation on the human's behalf.

## OpenSpec orchestration

An experimental custom schema bundle now lives at:

[`integrations/openspec/design-before-code/`](integrations/openspec/design-before-code/)

It orchestrates:

```text
proposal
  ↓
business-model
  ↓
ux-flow
  ↓
data-model
  ↓
design-readiness
  ↓
human-approval
  ↓
specs
  ↓
technical-design
  ↓
tasks
  ↓
apply
```

The four Design Before Code skills remain independently usable. OpenSpec owns artifact orchestration and keeps its native behavior-spec / technical-design / tasks / apply workflow after the product-design approval boundary.

`human-approval.md` is a human-only contract. Because OpenSpec dependency edges are artifact-availability relationships rather than actor-authenticated approvals, stronger organizational enforcement requires an external CI/hook/review mechanism. The schema does not pretend otherwise.

See the integration README for installation and lifecycle details.

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
│   ├── METHOD-SYNTHESIS.md
│   ├── EVALUATION-PROTOCOL.md
│   └── ROADMAP.md
├── skills/
│   ├── business-domain-design/
│   ├── ux-flow-design/
│   ├── data-model-design/
│   └── design-readiness-review/
├── integrations/
│   └── openspec/
│       └── design-before-code/
├── examples/
├── evals/
├── ATTRIBUTION.md
├── CHANGELOG.md
├── LICENSE
└── README.md
```

## Project rules

A failure found in one benchmark may enter a core Skill only after it is generalized beyond that specific domain. We optimize for reusable reasoning, not benchmark-specific answers.

Likewise, an upstream method enters a core Skill only after it is adapted to the Project Charter. We do not import ceremony merely because a respected upstream project uses it.

The readiness reviewer is a **gate and router**, not a universal fourth designer. If it finds a defect, the issue should be routed back to the owning layer: business semantics, UX flow, or data model.

## Status

Early experimental project — **4 of 4 core Skills have synthesized minimum usable versions, and the first OpenSpec orchestration bundle exists**.

Current priority: validate the custom schema with a real OpenSpec CLI, then run one controlled end-to-end greenfield change through Business → UX → Data → Readiness → human approval → specs/design/tasks.
