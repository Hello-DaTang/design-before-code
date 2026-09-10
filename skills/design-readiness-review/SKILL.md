---
name: design-readiness-review
description: Cross-review business, UX, and data-model artifacts for consistency and readiness before human approval and implementation.
---

# design-readiness-review

Cross-review business-domain, UX-flow, and data-model artifacts before implementation. The goal is not to summarize each artifact, but to determine whether they describe the same product and whether implementation can begin safely.

**Current behavior target: v0.2.**

This Skill synthesizes BMad-style implementation-readiness/cohesion review, Spec Kit-style phase gates/re-checks, and Superpowers-style explicit human approval before implementation.

## When to use

Use this skill after `business-domain-design`, `ux-flow-design`, and `data-model-design` have produced reviewable artifacts.

Use it before implementation planning, OpenSpec apply/tasks, database migration, API implementation, or frontend coding.

Do not use it as another architecture-design pass or as a way to silently resolve disagreements between artifacts.

## Non-negotiable rules

1. **Do not merely summarize the three artifacts. Compare them.**
2. **Do not fix contradictions by silently choosing one artifact as correct.** Surface the contradiction and identify who must decide.
3. **Do not mark READY while a consequential DECISION REQUIRED remains unresolved or was silently resolved downstream.**
4. **Do not assume UX behavior is implementable unless business lifecycle and data/API capabilities support it.**
5. **Do not assume a data constraint is valid unless the business model actually requires it.**
6. **Do not allow a core business task to exist without a coherent UX path and supporting data semantics.**
7. **Do not allow a UX promise such as draft, undo, partial completion, historical correction, or conflict recovery without identifying the downstream support it requires.**
8. **Do not start implementation if cross-artifact scenario simulation exposes a contradiction.**
9. **Re-run the gate after material upstream design changes; a previous READY does not automatically survive changed semantics.**
10. **Human approval is a hard gate: the agent cannot approve implementation on the user's behalf.**

## Required inputs

Prefer these artifacts when available:

- business/domain model;
- UX/task-flow behavioral contract;
- data-model design;
- unresolved decision lists from each artifact;
- original requirements/meeting notes for provenance checks when needed;
- project-level design constraints/constitution/context if the project defines one.

If one of the three core artifacts is missing, the default outcome is **NOT READY** unless the missing view is demonstrably irrelevant to the scoped change.

## Required output sequence

1. Artifact inventory, scope, and project constraints
2. Decision ledger reconciliation
3. Coverage-lens review
4. Concept and terminology alignment
5. Lifecycle/state/event consistency
6. Task-to-capability traceability
7. UX-to-data and data-to-business review
8. Cross-artifact scenario walkthrough
9. Contradiction/risk findings and next-owner routing
10. Implementation readiness + human approval gate

## 1. Artifact inventory, scope, and project constraints

State which artifacts are being reviewed, their versions/status, and the business scope they cover.

Do not compare artifacts that describe different scope boundaries as if they were inconsistent. Surface scope mismatch first.

If the project defines explicit constraints such as a constitution, ADRs, architecture principles, compliance requirements, tenant rules, platform limits, or the Design Before Code Project Charter, list the relevant ones and verify that later artifacts do not violate them.

This is analogous to a pre/post design gate: constraints must still hold **after** design decisions have been made.

## 2. Decision ledger reconciliation

Collect consequential statements from all artifacts and classify them using the shared provenance model:

- FACT
- INFERENCE
- RECOMMENDATION
- ASSUMPTION
- DECISION REQUIRED

Then check for drift:

- a DECISION REQUIRED in business design became a concrete UX behavior without approval;
- a DECISION REQUIRED in UX became a concrete database field/state without approval;
- an ASSUMPTION in one artifact appears as a FACT in another;
- two artifacts use different answers to the same unresolved question;
- a recommendation has silently become a constraint.

Create a compact table:

| Decision | Business | UX | Data | Status |
|---|---|---|---|---|
| Can production records be edited? | DECISION REQUIRED | Edit button shown | Mutable row + audit fields | CONTRADICTION / SILENT RESOLUTION |

Do not self-resolve these conflicts.

## 3. Coverage-lens review

Before deep semantic review, run compact coverage lenses inspired by mature planning-review workflows.

### Flow coverage

For every primary business goal/journey:

- is there a named actor/protagonist?
- is there a complete key flow?
- is there a success/climax point?
- is a failure/recovery path present where needed?

### Surface/state coverage

For every UX surface in the information architecture:

- which key flow reaches it?
- are applicable default/loading/empty/error/permission/conflict states specified?
- can the user recover from consequential failures?

### Concept/data coverage

For every durable business concept or event required by a primary task:

- is its identity/lifecycle represented by the data model when persistence is required?
- can the system preserve the history promised by Business/UX?

### Terminology inheritance

Check that important terms retain the same meaning and names across artifacts unless an explicit UX-facing alias is justified.

### Reference/constraint closure

Check that all referenced decisions, concepts, states, or project constraints resolve to an actual definition instead of relying on “implementation will figure it out.”

Coverage review catches omissions before judgment about elegance.

## 4. Concept and terminology alignment

Check that the same business terms mean the same thing across artifacts.

Look for:

- one concept split into multiple names;
- two different concepts accidentally using one name;
- UX labels that hide important business distinctions;
- data entities that have no corresponding business meaning;
- business concepts required for a user task but absent from the data model.

Examples:

- `Plan` and `Actual` are separate business lifecycles, but UX/data both call them `ProductionRecord`;
- `Delete` in UX actually means business `Void`, while data model uses technical soft delete;
- `Current Version` means “effective today” in business but “manually selected pointer” in data.

## 5. Lifecycle, state, and event consistency

For every consequential concept, compare:

- business lifecycle;
- UX actions/transitions;
- data states/history representation.

Check that:

- every UX action corresponds to a valid business transition;
- every business transition has a usable interaction path when users must perform it;
- data model can represent the transition/history promised by the business/UX;
- UX does not expose transitions forbidden by business rules;
- data model does not encode states/transitions nobody defined.

Explicitly distinguish state from event.

Example:

- Business: Payment succeeds, causing Order to become Paid.
- UX: User retries payment after failure.
- Data: One mutable `payment_status` field only.

This may indicate missing Payment-attempt/event history depending on requirements.

## 6. Task-to-capability traceability

For each primary user task, trace:

```text
User task
   ↓
Business goal/rule/lifecycle
   ↓
UX journey/action/state
   ↓
Required data/API capability
```

Mark each task:

- SUPPORTED
- PARTIALLY SUPPORTED
- BLOCKED
- CONTRADICTED

Examples of capability gaps:

- UX allows “Save Draft” but no draft lifecycle/data representation exists;
- UX allows partial shipment but business/data assume one shipment per order;
- business requires backdated correction but UX always defaults current master data;
- data supports multiple versions but UX never exposes which version applies.

## 7. UX-to-data and data-to-business review

### UX → downstream support

Review every consequential UX promise and ask what downstream support it requires.

Common promises to check:

- save/resume draft;
- optimistic conflict recovery;
- undo/revert;
- cancellation after partial progress;
- partial fulfillment;
- multi-payment/refund;
- historical/backdated entry;
- approval/rejection with audit trail;
- version/effective-date selection;
- preserving historical labels/values;
- bulk operations with partial failure.

If UX behavior depends on unsupported data semantics, mark it BLOCKED instead of assuming implementation will solve it later.

### Data → business justification

For every consequential data-model mechanism, verify its business justification.

Challenge:

- history/version tables with no business history requirement;
- status values not represented in business lifecycle;
- uniqueness constraints stricter than the business rules;
- soft delete used where business semantics require void/cancel/archive;
- snapshots not required by historical display/calculation policy;
- duplicated fields whose source-of-truth semantics are not visible in UX/business;
- generic configuration abstractions with no business concept;
- technical audit fields treated as if they satisfy business audit requirements.

A technically valid schema can still be product-wrong.

## 8. Cross-artifact scenario walkthrough

Run at least one realistic scenario across all three artifacts.

Use a compact matrix:

| Step | Business truth | User sees/does | Data must preserve/change | Result |
|---|---|---|---|---|

Include at least one adversarial step when relevant:

- late/backdated entry;
- partial completion then cancellation;
- concurrent edit;
- policy/version/price change;
- master-data rename;
- correction after approval;
- partial shipment/refund/payment;
- permission change.

The scenario must verify not only that each artifact works alone, but that all three agree on the same semantics.

If one step cannot be represented consistently, the design is not READY.

## 9. Contradiction/risk findings and next-owner routing

Group findings by severity:

### BLOCKER

Implementation should not start because business/UX/data semantics conflict or a consequential decision is unresolved.

### MAJOR

The design is coherent in principle but an important task/state/recovery/history path is unsupported or underspecified.

### MINOR

Naming, secondary flow, or implementation detail can be clarified without materially changing the model.

For every BLOCKER/MAJOR finding include:

- conflicting artifacts/sections;
- what the user/business would experience;
- why implementation cannot safely guess;
- which owner should decide or which upstream artifact should be revised.

Route findings back to the owning layer:

```text
Business semantics/lifecycle → business-domain-design
Task flow/state/recovery     → ux-flow-design
Persistence/history/integrity → data-model-design
Cross-artifact conflict      → re-run design-readiness-review after fixes
```

The readiness skill is a gate and router, not a fourth universal designer.

## 10. Implementation readiness + human approval gate

Choose exactly one machine-readable readiness result:

### READY_FOR_HUMAN_APPROVAL

All primary tasks trace cleanly across business, UX, and data; no material contradiction or unresolved consequential decision remains.

This is **not** permission to implement yet. Present the readiness report and wait for explicit human approval.

### NEEDS_DECISION

The design direction is coherent, but one or more named human decisions still block safe implementation.

### NOT_READY

Artifacts are missing, scope is inconsistent, project constraints are violated, or contradictions require upstream redesign before a meaningful implementation plan can be created.

End with a concise blocking list and next-owner map.

Only after the human explicitly approves a `READY_FOR_HUMAN_APPROVAL` design may the workflow proceed into tasks/planning/implementation.

If any material artifact changes afterward, invalidate the prior readiness result and re-run this review.

## Review principles

- Cross-artifact consistency is more important than the elegance of any single artifact.
- Mechanical coverage comes before subjective judgment: missing flows/states/concepts should be found explicitly.
- The reviewer finds contradictions; it does not become a fourth place that invents product semantics.
- Every user-facing promise must have business meaning and technical/data support.
- Every consequential schema rule must trace back to business meaning.
- Decisions should have one visible provenance, not mutate silently across documents.
- Project-level constraints must still pass after design, not only before it.
- Scenario walkthroughs are the strongest way to expose disagreements hidden by terminology.
- Readiness is invalidated by material upstream changes and must be re-checked.
- Human approval, not agent confidence, is the final pre-implementation gate.