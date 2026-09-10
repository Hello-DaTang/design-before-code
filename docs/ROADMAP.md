# Design Before Code — Roadmap

This roadmap follows `docs/PROJECT-CHARTER.md`: the project exists to make consequential business, UX, and data-model decisions reviewable before implementation.

Method evolution must also follow [`docs/METHOD-SYNTHESIS.md`](METHOD-SYNTHESIS.md): core Skills should synthesize proven upstream mechanisms rather than evolve as isolated prompts or copied templates.

## Current state

The first four-skill loop has synthesized minimum-usable versions:

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
- OpenSpec custom schema bundle under `integrations/openspec/design-before-code/`

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

**Complete at minimum-usable level.**

- design-readiness-review v0.2 exists
- compact contradiction evals exist
- synthesis smoke test passed 4/4
- readiness result is explicitly separate from human approval

### Phase C — OpenSpec orchestration

**Schema CLI validation complete; runtime agent validation is next.**

Current bundle:

```text
integrations/openspec/design-before-code/
├── schema.yaml
├── README.md
├── INTEGRATION.md
└── templates/
```

Implemented flow:

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

Validated locally with OpenSpec 1.8.0 on 2026-09-10:

- project schema discovery succeeded;
- `openspec schema validate design-before-code --verbose` passed YAML, structure, template, and dependency-graph checks;
- `openspec schema which design-before-code` resolved the project-local bundle;
- `openspec new change dbc-smoke --schema design-before-code` succeeded;
- `openspec status --change dbc-smoke` showed 9 artifacts and the expected blocked dependency graph.

Design choices:

- keep each core Skill independently usable;
- precheck companion Skill availability instead of silently falling back;
- retain OpenSpec delta specs, technical design, task tracking, apply/verify/archive after approval;
- treat `human-approval.md` as a human-only operational contract;
- re-check readiness/approval in specs, tasks, and apply instructions;
- acknowledge that OpenSpec `requires` edges are artifact-availability relationships, not actor-authenticated business gates;
- leave identity-level enforcement to optional external CI/hook/review integration.

Next runtime validation steps:

1. install/use the schema in an agent-enabled OpenSpec project that can access the four Design Before Code Skills;
2. create one small test change;
3. verify `proposal → business-model → ux-flow → data-model → design-readiness` invokes the intended Skills rather than reproducing their logic from schema prompts;
4. verify the agent stops at `human-approval` and does not create or edit that artifact itself;
5. verify PENDING/missing approval prevents specs/tasks/apply at the instruction level;
6. after manual `APPROVAL: APPROVED`, verify specs → technical-design → tasks unlock and proceed;
7. fix only observed integration failures.

### Phase D — End-to-end milestone validation

After runtime orchestration passes, run one realistic greenfield requirement through:

```text
requirements
→ proposal
→ business-model
→ ux-flow
→ data-model
→ design-readiness
→ human approval
→ specs
→ technical-design
→ tasks
```

This is a Level 3 Domain Benchmark, not a routine development loop.

Measure primarily:

- whether artifacts stay semantically aligned;
- whether unresolved decisions remain visible;
- whether UX promises have business/data support;
- whether readiness catches cross-artifact drift;
- whether the human understands what is being approved;
- whether OpenSpec preserves the approval boundary before implementation;
- whether upstream-inspired mechanisms improve behavior without adding unnecessary ceremony.

### Phase E — Stronger enforcement and broader confidence

Only after the first end-to-end loop is stable:

- decide whether optional CI/hook approval enforcement is worth adding;
- add milestone cross-domain benchmarks;
- improve installation/use documentation;
- refine Skill interoperability and distribution;
- periodically review mature upstream methods for transferable improvements;
- consider early-MVP review mode;
- consider a separate brownfield/reverse-engineering project or skill family if justified.

## Near-term maintenance policy

Until Phase C/D reveals a general issue:

- `business-domain-design v0.2.x`: compact correctness fixes only.
- `ux-flow-design v0.2.x`: compact correctness fixes only.
- `data-model-design v0.2.x`: correctness and domain-general regression fixes only.
- `design-readiness-review v0.2.x`: cross-artifact correctness and routing fixes only.
- OpenSpec schema: fix only verified orchestration, compatibility, or gate failures; do not duplicate Skill logic into schema instructions.

## What we should not do next

- Keep polishing any one Skill until it becomes a giant prompt.
- Copy upstream SKILL.md files or templates into this repository and rename them.
- Re-invent mature planning/domain/UX/schema mechanisms without first checking established approaches.
- Import DDD, visual-design-system, or architecture ceremony without a user-problem reason.
- Add Figma/design-system generation before task-flow reasoning has been validated end to end.
- Build a full legacy/brownfield reverse-engineering framework.
- Require repeated no-skill A/B tests for routine edits.
- Let `design-readiness-review` silently fix product decisions rather than route them upstream.
- Let OpenSpec become the product identity; it remains the orchestration layer.
- Claim schema dependencies alone provide authenticated human approval.

## Definition of the first meaningful milestone

The first meaningful milestone is reached when a user can provide rough meeting notes and obtain, before coding:

1. a business/domain model they can understand;
2. a page/task flow they can challenge;
3. a data model they can review;
4. a cross-artifact readiness report;
5. an explicit human approval gate;
6. orchestration that preserves those gates before tasks/apply;
7. one end-to-end real-project validation.

Items 1–5 exist at an experimental synthesized level. OpenSpec schema discovery/validation and dependency-graph checks for item 6 have passed; runtime Skill invocation and approval-stop behavior are the remaining Phase C checks. Item 7 follows after those pass.
