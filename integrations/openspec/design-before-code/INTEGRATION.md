# Design Before Code × OpenSpec integration

## Why this shape

OpenSpec already handles change artifacts, delta behavior specs, technical design, task tracking, implementation, verification, and archive well. Design Before Code should not replace those strengths.

This schema adds the missing pre-implementation review layers:

```text
change intent
→ business semantics
→ user-task flow
→ data semantics/history
→ cross-artifact readiness
→ human approval
→ OpenSpec behavior specs
→ technical design
→ tasks/apply
```

## Dependency graph

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

`technical-design` also reads `data-model` directly. `tasks` and `apply` re-check `human-approval` even though it is already a transitive dependency.

## Semantic gates vs OpenSpec dependency edges

OpenSpec `requires` edges determine which artifacts are available to create. They are not content-aware business gates.

Therefore this schema uses two layers:

1. **Dependency layer** — artifact ordering and context availability.
2. **Semantic layer** — artifact instructions explicitly refuse to proceed when an upstream decision/readiness/approval condition is not satisfied.

The final human gate uses two exact markers:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
APPROVAL: APPROVED
```

The second marker is written by a human, never by the AI agent.

This is an operational agent contract. It is not identity-authenticated enforcement. Teams requiring stronger governance should add CI, hooks, CODEOWNERS/review rules, or another approval service.

## Artifact responsibilities

### proposal

Captures change intent, scope, actors, constraints, source material, and candidate capability areas. It does not solve product/UX/data design.

### business-model

Delegates to `business-domain-design` and owns actors, terminology, business concepts, lifecycles/events, rules, scenarios, provenance, and business decisions.

### ux-flow

Delegates to `ux-flow-design` and owns task-first journeys, information architecture, surface/state contracts, recovery behavior, and UX-created downstream requirements.

### data-model

Delegates to `data-model-design` and owns conceptual/logical/physical candidates, integrity, temporal semantics, redundancy, canonical units, and scenario-tested history behavior.

### design-readiness

Delegates to `design-readiness-review` and checks that Business ↔ UX ↔ Data describe the same system. It is a gate/router, not another designer.

### human-approval

Human-only contract. AI must not create or alter it.

### specs

Converts the approved product design into OpenSpec delta behavior requirements/scenarios. This remains the canonical OpenSpec behavior-contract layer.

### technical-design

Explains how to implement the approved behavior. It may make technical decisions, but must not silently reopen product semantics.

### tasks

Creates trackable implementation work from approved specs and technical design.

## Decision handling

A normal low-ambiguity change can flow to one final human approval.

When a material `DECISION REQUIRED` appears, stop at the earliest layer that owns it:

```text
business policy/lifecycle → business-model
interaction/state/recovery → ux-flow
identity/history/integrity → data-model
cross-artifact contradiction → design-readiness routes upstream
```

After a human changes any approved semantic artifact, downstream artifacts must be reconciled and `design-readiness` must run again. Existing human approval is stale whenever the approved semantics materially change.

## Suggested commands

```bash
# create the change
openspec new change my-feature --schema design-before-code

# inspect what is ready
openspec status --change my-feature

# create/review one artifact at a time during design
/opsx:continue my-feature

# after human approval, continue through specs/design/tasks
/opsx:continue my-feature

# implement only when tasks + approval checks are satisfied
/opsx:apply my-feature
```

Use fast-forward only when you are certain no human interaction is required before the target artifact set. A workflow containing a human-only artifact should normally use `continue` around the approval boundary.

## Validation

After installation:

```bash
openspec schema validate design-before-code
openspec schema which design-before-code
```

For a smoke test, create a disposable change and inspect:

```bash
openspec status --change smoke-test --json
openspec instructions business-model --change smoke-test --json
```

Confirm the returned artifact instruction contains the companion-skill precheck and expected dependencies.
