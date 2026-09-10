# Design Before Code — Roadmap

This roadmap follows `docs/PROJECT-CHARTER.md`: the project exists to make consequential business, UX, and data-model decisions reviewable before implementation.

## Current state

The first four-skill loop now has minimum usable versions:

- `business-domain-design` — v0.1
- `ux-flow-design` — v0.1
- `data-model-design` — v0.2.1
- `design-readiness-review` — v0.1

Infrastructure:

- Project Charter
- low-cost Evaluation Protocol
- per-skill regression evals
- manufacturing example
- OpenSpec integration notes

## Core skill set

### 1. `business-domain-design`

Answers: **What business world are we actually building?**

Turns notes/requirements into actors, terminology, concepts, lifecycles, states/events, rules, scenarios, decision provenance, and downstream constraints without forcing ritual DDD.

### 2. `ux-flow-design`

Answers: **How does a human accomplish the business task naturally?**

Designs task-first information architecture, journeys, surface responsibilities, failure/recovery states, historical/backdated interaction behavior, and low-fidelity wireframes before frontend implementation.

### 3. `data-model-design`

Answers: **What data exists, why does it exist, and will history still mean the right thing later?**

Designs conceptual/logical/physical candidates, integrity, redundancy, temporal semantics, scenario validation, and a human decision gate.

### 4. `design-readiness-review`

Answers: **Do the business model, user flow, and data model describe the same system?**

Cross-checks decision provenance, terminology, lifecycles, task traceability, UX promises, data constraints, historical semantics, and a cross-artifact scenario; produces READY / NEEDS DECISION / NOT READY.

## Development phases

### Phase A — Complete the design triangle

**Complete at minimum-usable level.**

- business-domain-design v0.1
- ux-flow-design v0.1
- data-model-design v0.2.1

### Phase B — Cross-artifact review

**Initial implementation complete.**

- design-readiness-review v0.1 exists
- compact contradiction evals exist

Next, run low-cost cross-artifact evals to ensure the reviewer routes defects to the correct owning layer rather than becoming a universal designer.

### Phase C — OpenSpec orchestration

**Next major phase.**

Create an OpenSpec workflow/schema where artifacts depend on one another:

```text
proposal / requirements
        ↓
business-model
        ↓
ux-flow
        ↓
data-model
        ↓
design-readiness-review
        ↓
human approval
        ↓
tasks / implementation
```

Goals:

- keep each core Skill independently usable;
- make artifact dependencies explicit;
- prevent `tasks` / implementation from starting while readiness is NEEDS DECISION or NOT READY;
- prevent the agent from self-approving human decisions;
- allow upstream artifact revisions when readiness review routes a defect back.

### Phase D — End-to-end milestone validation

After orchestration exists, run one realistic greenfield requirement through all four Skills.

This is a Level 3 Domain Benchmark, not a routine development loop.

Measure primarily:

- whether artifacts stay semantically aligned;
- whether unresolved decisions remain visible;
- whether UX promises have business/data support;
- whether the readiness reviewer catches cross-artifact drift;
- whether the human can understand and approve the design before coding.

### Phase E — Broaden confidence

Only after the first end-to-end loop is stable:

- add milestone cross-domain benchmarks;
- improve installation/use documentation;
- refine Skill interoperability;
- consider early-MVP review mode;
- consider a separate brownfield/reverse-engineering project or skill family if justified.

## Near-term maintenance policy

Until Phase C/D reveals a general issue:

- `business-domain-design v0.1.x`: compact correctness/regression fixes only.
- `ux-flow-design v0.1.x`: compact correctness/regression fixes only.
- `data-model-design v0.2.x`: correctness and domain-general regression fixes only.
- `design-readiness-review v0.1.x`: focus on cross-artifact consistency and routing defects to the right layer.

Do not substantially expand one Skill merely because it failed a single domain-specific benchmark.

## What we should not do next

- Keep polishing any one Skill until it becomes a giant prompt.
- Add Figma/design-system generation before task-flow reasoning has been validated end to end.
- Build a full legacy/brownfield reverse-engineering framework.
- Require repeated no-skill A/B tests for routine edits.
- Let `design-readiness-review` silently fix product decisions rather than route them upstream.
- Let OpenSpec become the product identity; it is the orchestration layer.

## Definition of the first meaningful milestone

The first meaningful milestone is reached when a user can provide rough meeting notes and obtain, before coding:

1. a business/domain model they can understand;
2. a page/task flow they can challenge;
3. a data model they can review;
4. a cross-artifact readiness report;
5. an explicit human approval gate;
6. orchestration that prevents implementation from starting before those gates are satisfied.

The four design Skills now cover items 1–5 at an experimental/minimum-usable level. The next milestone work is item 6: orchestration and one end-to-end validation.
