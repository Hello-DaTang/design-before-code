# Design Before Code — Roadmap

This roadmap follows `docs/PROJECT-CHARTER.md`: the project exists to make consequential business, UX, and data-model decisions reviewable before implementation.

Method evolution must also follow [`docs/METHOD-SYNTHESIS.md`](METHOD-SYNTHESIS.md): core Skills should synthesize proven upstream mechanisms rather than evolve as isolated prompts or copied templates.

## Current state

The first four-skill loop now has synthesized minimum-usable versions:

- `business-domain-design` — v0.2
- `ux-flow-design` — v0.2
- `data-model-design` — v0.2.1
- `design-readiness-review` — v0.2

Infrastructure:

- Project Charter / anti-drift rules
- Method Synthesis / upstream influence map
- low-cost Evaluation Protocol
- per-skill regression evals
- manufacturing example
- OpenSpec integration notes

## Core skill set

### 1. `business-domain-design`

Answers: **What business world are we actually building?**

Synthesizes collaborative design gating, complexity-sensitive planning, and selected DDD discovery techniques. It chooses Plain CRUD / Light Domain Modeling / Deep Domain Discovery before using ceremony, and uses Event Storming-lite / ubiquitous language only where they clarify real complexity.

### 2. `ux-flow-design`

Answers: **How does a human accomplish the business task naturally?**

Focuses on the behavioral experience contract: named-protagonist journeys, task-first information architecture, surface responsibilities, state/recovery coverage, low-fidelity wireframes, accessibility floor, and surface closure. Visual-brand/design-system work remains separate.

### 3. `data-model-design`

Answers: **What data exists, why does it exist, and will history still mean the right thing later?**

Synthesizes explicit data-model artifacts with normalization, constraints, access-pattern-driven physical design, temporal semantics, scenario validation, and human decision locks.

### 4. `design-readiness-review`

Answers: **Do the business model, user flow, and data model describe the same system?**

Synthesizes readiness/cohesion review, staged gates, and explicit human approval. It runs coverage lenses, reconciles decisions, traces primary tasks across Business → UX → Data, re-checks project constraints after design, and routes defects back to the owning Skill.

## Development phases

### Phase A — Complete and synthesize the design triangle

**Complete at minimum-usable level.**

- business-domain-design v0.2
- ux-flow-design v0.2
- data-model-design v0.2.1
- upstream method synthesis documented

### Phase B — Cross-artifact review

**Initial implementation complete.**

- design-readiness-review v0.2 exists
- compact contradiction evals exist
- readiness result requires explicit human approval before implementation

Before OpenSpec work expands, use low-cost Unit/Regression tests to check that the newly synthesized behaviors are actually followed and that no Skill became unnecessarily ceremonial.

### Phase C — OpenSpec orchestration

**Next major phase after compact synthesis regressions.**

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
explicit human approval
        ↓
tasks / implementation
```

Goals:

- keep each core Skill independently usable;
- make artifact dependencies explicit;
- prevent `tasks` / implementation from starting while readiness is NEEDS_DECISION or NOT_READY;
- distinguish `READY_FOR_HUMAN_APPROVAL` from actual human approval;
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
- whether the human can understand and approve the design before coding;
- whether upstream-inspired mechanisms improve behavior without adding unnecessary ceremony.

### Phase E — Broaden confidence

Only after the first end-to-end loop is stable:

- add milestone cross-domain benchmarks;
- improve installation/use documentation;
- refine Skill interoperability;
- periodically review mature upstream methods for transferable improvements;
- consider early-MVP review mode;
- consider a separate brownfield/reverse-engineering project or skill family if justified.

## Near-term maintenance policy

Until Phase C/D reveals a general issue:

- `business-domain-design v0.2.x`: test the modeling-depth gate, event-first discovery, and anti-DDD-overkill behavior.
- `ux-flow-design v0.2.x`: test named journeys, surface/state closure, and behavior-before-visual discipline.
- `data-model-design v0.2.x`: correctness and domain-general regression fixes only.
- `design-readiness-review v0.2.x`: test coverage lenses, re-gating, human-approval separation, and defect routing.

Do not substantially expand one Skill merely because it failed a single domain-specific benchmark or because an upstream method contains a sophisticated artifact we do not need.

## What we should not do next

- Keep polishing any one Skill until it becomes a giant prompt.
- Copy upstream SKILL.md files or templates into this repository and rename them.
- Re-invent mature planning/domain/UX/schema mechanisms without first checking established approaches.
- Import DDD, visual-design-system, or architecture ceremony without a user-problem reason.
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

The four design Skills now cover items 1–5 at an experimental/synthesized minimum-usable level. The next milestone work is compact regression of the synthesis changes, then item 6: orchestration and one end-to-end validation.
