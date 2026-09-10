# Design Before Code — Roadmap

This roadmap follows `docs/PROJECT-CHARTER.md`: the project is not a database-only tool. Its purpose is to make consequential business, UX, and data-model decisions reviewable before implementation.

## Current state

Implemented minimum usable versions:

- `business-domain-design` — v0.1
- `ux-flow-design` — v0.1
- `data-model-design` — v0.2.1

Infrastructure:

- Project Charter
- low-cost Evaluation Protocol
- per-skill regression evals
- manufacturing example
- OpenSpec integration notes

Missing from the first complete loop:

- `design-readiness-review` v0.1

## Target skill set

The first complete product loop contains four skills.

### 1. `business-domain-design`

Purpose: convert meeting notes, rough requirements, stakeholder explanations, and partial understanding into a reviewable business model before database or UI design.

Primary outputs:

- actors and goals;
- terminology / ubiquitous language without requiring users to know DDD jargon;
- business concepts and responsibilities;
- lifecycle and state changes;
- business events;
- rules and invariants;
- normal and exceptional flows;
- FACT / INFERENCE / RECOMMENDATION / ASSUMPTION / DECISION REQUIRED;
- explicit questions that materially affect downstream UX or data design.

Non-goal: automatically force full DDD, aggregates, event sourcing, or bounded contexts onto simple CRUD systems.

This skill answers: **What business world are we actually building?**

### 2. `ux-flow-design`

Purpose: make the user's path through the product visible before pages/components are implemented.

Primary outputs:

- actor jobs and task priorities;
- information architecture based on user goals rather than backend entities;
- primary journeys and decision points;
- page/surface responsibilities;
- page vs dialog/drawer decisions;
- normal path and recovery path;
- empty/loading/error/permission/conflict states;
- low-fidelity wireframes;
- downstream API/data implications created by UX promises;
- explicit interaction decisions that remain unresolved.

Non-goal: replace Figma or become a visual branding/design-system generator.

This skill answers: **How does a human accomplish the business task naturally?**

### 3. `data-model-design`

Purpose: turn approved business semantics into a reviewable conceptual, logical, physical, temporal, and scenario-tested data model.

Current implementation exists and will continue to evolve through compact regression cases rather than repeated full A/B experiments.

This skill answers: **What data exists, why does it exist, and will history still mean the right thing later?**

### 4. `design-readiness-review`

Purpose: cross-review the three design views before implementation.

It should detect contradictions such as:

- UX expects an edit/recovery behavior that the lifecycle model forbids;
- business model says Plan and Actual have separate lifecycles while the data model collapses them;
- data model supports multiple versions but the UX hides the version choice;
- UX requires historical display semantics not preserved by the data model;
- a DECISION REQUIRED item was silently resolved in another artifact;
- a core user task has no supported data/business path;
- UX promises draft/conflict/recovery behavior that downstream APIs/data cannot support;
- data model encodes a state transition the business model never defined.

Primary output:

- consistency findings;
- blocked decisions;
- scenario walkthrough across business + UX + data;
- READY / NEEDS DECISION / NOT READY implementation gate.

This skill answers: **Do the business model, user flow, and data model describe the same system?**

## Development sequence

### Phase A — Complete the design triangle

Status: **complete at minimum-usable level**.

1. `business-domain-design` v0.1 — done.
2. `ux-flow-design` v0.1 — done.
3. `data-model-design` v0.2.1 — done.
4. Each has compact unit/regression evals.

Do not try to make any one skill perfect before the cross-artifact reviewer exists.

### Phase B — Cross-artifact review

Current phase.

5. Build `design-readiness-review` v0.1.
6. Add compact evals for contradictions across artifacts.
7. Use one realistic greenfield scenario to run:

```text
requirements / meeting notes
        ↓
business-domain-design
        ↓
ux-flow-design
        ↓
data-model-design
        ↓
design-readiness-review
        ↓
human approval
```

The first end-to-end test should focus on cross-artifact consistency, not on maximizing the quality of any one document.

### Phase C — OpenSpec orchestration

8. Create an OpenSpec custom schema/integration that treats these artifacts as dependencies.
9. Block implementation until `design-readiness-review` is resolved and human approval exists.
10. Keep each skill independently usable outside OpenSpec.

### Phase D — Broaden confidence

11. Add cross-domain benchmarks only at milestones.
12. Improve documentation and installation guidance.
13. Consider early-MVP review mode after greenfield behavior is stable.

## Near-term version plan

### `business-domain-design v0.1.x`

Maintenance only until cross-artifact review reveals a general gap.

### `ux-flow-design v0.1.x`

Maintenance only until cross-artifact review reveals a general gap.

### `data-model-design v0.2.x`

Maintenance only:

- correctness fixes;
- compact regression cases;
- no more manufacturing-specific expansion unless the rule generalizes.

### `design-readiness-review v0.1`

Next new skill.

Minimum usable behavior:

- consumes business-domain, UX-flow, and data-model artifacts;
- compares semantics instead of summarizing them independently;
- traces every major user task to a business rule/lifecycle and supporting data capability;
- checks every material data constraint against the business model;
- checks UX promises such as draft, correction, conflict recovery, historical entry, partial fulfillment, and destructive actions against business/data support;
- detects DECISION REQUIRED items that another artifact silently resolved;
- runs at least one cross-artifact scenario;
- produces READY / NEEDS DECISION / NOT READY plus a concise blocking list.

## What we should not do next

- Keep polishing one Skill until it becomes a giant prompt.
- Build OpenSpec orchestration before `design-readiness-review` exists.
- Create a full brownfield reverse-engineering skill.
- Add Figma/design-system implementation before task-flow reasoning is stable.
- Require repeated no-skill A/B tests for routine changes.
- Treat cross-artifact inconsistencies as a reason to immediately expand all three upstream Skills; first decide which layer owns the missing reasoning.

## Definition of the first meaningful milestone

The project reaches its first meaningful multi-skill milestone when a user can provide rough meeting notes and obtain, before coding:

1. a business/domain model they can understand;
2. a page/task flow they can challenge;
3. a data model they can review;
4. a cross-artifact readiness report;
5. an explicit human gate before implementation.

At that point the project solves the original 0→1 problem end-to-end rather than solving only database or page design.
