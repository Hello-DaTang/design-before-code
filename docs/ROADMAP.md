# Design Before Code — Roadmap

This roadmap follows `docs/PROJECT-CHARTER.md`: the project is not a database-only tool. Its purpose is to make consequential business, UX, and data-model decisions reviewable before implementation.

## Current state

Implemented:

- `data-model-design` — current experimental line: v0.2.1

Infrastructure:

- Project Charter
- low-cost Evaluation Protocol
- regression evals
- manufacturing example
- OpenSpec integration notes

## Target skill set

The first complete product loop should contain four skills.

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

This skill should answer: **What business world are we actually building?**

### 2. `ux-flow-design`

Purpose: make the user's path through the product visible before pages/components are implemented.

Primary outputs:

- user goal and entry point;
- task flow / journey;
- information architecture;
- primary action per surface;
- normal path and recovery path;
- empty/loading/error/permission/conflict states;
- low-fidelity wireframes where useful;
- interaction alternatives when a major workflow decision is unresolved;
- explicit UX decisions that affect the data model or business lifecycle.

Non-goal: replace Figma or become a visual branding/design-system generator.

This skill should answer: **How does a human accomplish the business task naturally?**

### 3. `data-model-design`

Purpose: turn approved business semantics into a reviewable conceptual, logical, physical, temporal, and scenario-tested data model.

Current implementation exists and will continue to evolve through compact regression cases rather than repeated full A/B experiments.

This skill should answer: **What data exists, why does it exist, and will history still mean the right thing later?**

### 4. `design-readiness-review`

Purpose: cross-review the three design views before implementation.

It should detect contradictions such as:

- UX expects an edit/recovery behavior that the lifecycle model forbids;
- business model says Plan and Actual have separate lifecycles while the data model collapses them;
- data model supports multiple versions but the UX hides the version choice;
- UX requires historical display semantics not preserved by the data model;
- a DECISION REQUIRED item was silently resolved in another artifact;
- a core user task has no supported data/business path.

Primary output:

- consistency findings;
- blocked decisions;
- scenario walkthrough across business + UX + data;
- READY / NEEDS DECISION / NOT READY implementation gate.

This skill should answer: **Do the business model, user flow, and data model describe the same system?**

## Development sequence

### Phase A — Complete the design triangle

1. Stabilize `data-model-design` at the current low-cost regression level.
2. Build `business-domain-design` v0.1.
3. Build `ux-flow-design` v0.1.
4. Test each with compact unit/regression evals.

Do not try to make any one skill perfect before the other views exist.

### Phase B — Cross-artifact review

5. Build `design-readiness-review` v0.1.
6. Use one realistic greenfield scenario to run:

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

### Phase C — OpenSpec orchestration

7. Create an OpenSpec custom schema/integration that treats these artifacts as dependencies.
8. Block implementation until `design-readiness-review` is resolved and human approval exists.
9. Keep each skill independently usable outside OpenSpec.

### Phase D — Broaden confidence

10. Add cross-domain benchmarks only at milestones.
11. Improve documentation and installation guidance.
12. Consider early-MVP review mode after greenfield behavior is stable.

## Near-term version plan

### `data-model-design v0.2.x`

Maintenance only:

- correctness fixes;
- compact regression cases;
- no more manufacturing-specific expansion unless the rule generalizes.

### `business-domain-design v0.1`

Next new skill.

Minimum usable behavior:

- does not jump from notes to architecture/code;
- separates fact/inference/recommendation/assumption/decision;
- identifies actors, goals, concepts, lifecycles, events, rules, and exceptions;
- challenges missing or conflated concepts;
- avoids ritual DDD;
- produces a short human-review gate for downstream design.

### `ux-flow-design v0.1`

After business-domain-design.

Minimum usable behavior:

- task-first rather than page-first;
- shows normal + failure/recovery flows;
- checks repeated input, hidden dependencies, excessive navigation, and ambiguous next actions;
- exposes unresolved interaction decisions;
- produces low-fidelity structure without pretending visual polish is solved.

### `design-readiness-review v0.1`

After all three design artifacts exist.

Minimum usable behavior:

- cross-checks semantics instead of repeating each artifact;
- runs at least one scenario across all three views;
- blocks implementation on contradictions or unresolved consequential decisions.

## What we should not do next

- Keep polishing `data-model-design` until it becomes a giant prompt.
- Build OpenSpec orchestration before the other design skills exist.
- Create a full brownfield reverse-engineering skill.
- Add Figma/design-system implementation before task-flow reasoning is stable.
- Require repeated no-skill A/B tests for routine changes.

## Definition of the first meaningful milestone

The project reaches its first meaningful multi-skill milestone when a user can provide rough meeting notes and obtain, before coding:

1. a business/domain model they can understand;
2. a page/task flow they can challenge;
3. a data model they can review;
4. a cross-artifact readiness report;
5. an explicit human gate before implementation.

At that point the project solves the original 0→1 problem end-to-end rather than solving only database design.
