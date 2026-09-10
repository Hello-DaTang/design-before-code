# design-readiness-review

Cross-review business-domain, UX-flow, and data-model artifacts before implementation. The goal is not to summarize each artifact, but to determine whether they describe the same product and whether implementation can begin safely.

**Current behavior target: v0.1.**

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

## Required inputs

Prefer these artifacts when available:

- business/domain model;
- UX/task-flow design;
- data-model design;
- unresolved decision lists from each artifact;
- original requirements/meeting notes for provenance checks when needed.

If one of the three core artifacts is missing, the default outcome is **NOT READY** unless the missing view is demonstrably irrelevant to the scoped change.

## Required output sequence

1. Artifact inventory and scope
2. Decision ledger reconciliation
3. Concept and terminology alignment
4. Lifecycle/state/event consistency
5. Task-to-capability traceability
6. UX-to-data support review
7. Data-to-business justification review
8. Cross-artifact scenario walkthrough
9. Contradiction and risk findings
10. Implementation readiness gate

## 1. Artifact inventory and scope

State which artifacts are being reviewed, their versions/status, and the business scope they cover.

Do not compare artifacts that describe different scope boundaries as if they were inconsistent. Surface scope mismatch first.

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
- two artifacts use different answers to the same unresolved question.

Create a compact table:

| Decision | Business | UX | Data | Status |
|---|---|---|---|---|
| Can production records be edited? | DECISION REQUIRED | Edit button shown | Mutable row + audit fields | CONTRADICTION / SILENT RESOLUTION |

Do not self-resolve these conflicts.

## 3. Concept and terminology alignment

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

## 4. Lifecycle, state, and event consistency

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

## 5. Task-to-capability traceability

For each primary user task, trace:

```text
User task
   ↓
Business goal/rule/lifecycle
   ↓
UX journey/action
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

## 6. UX-to-data support review

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

## 7. Data-to-business justification review

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

## 9. Contradiction and risk findings

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

Do not solve everything inside this review skill. Route the issue back to the correct design layer.

## 10. Implementation readiness gate

Choose exactly one:

### READY

All primary tasks trace cleanly across business, UX, and data; no material contradiction or unresolved consequential decision remains.

### NEEDS DECISION

The design direction is coherent, but one or more named human decisions still block safe implementation.

### NOT READY

Artifacts are missing, scope is inconsistent, or contradictions require upstream redesign before a meaningful implementation plan can be created.

End with a concise blocking list and next-owner map, for example:

```text
B1 → business-domain-design: decide whether partial refund is allowed.
B2 → ux-flow-design: add recovery for concurrent edit conflict.
B3 → data-model-design: represent draft lifecycle promised by UX.
```

A human must explicitly approve the resolved design before implementation begins.

## Review principles

- Cross-artifact consistency is more important than the elegance of any single artifact.
- The reviewer finds contradictions; it does not become a fourth place that invents product semantics.
- Every user-facing promise must have business meaning and technical/data support.
- Every consequential schema rule must trace back to business meaning.
- Decisions should have one visible provenance, not mutate silently across documents.
- Scenario walkthroughs are the strongest way to expose disagreements hidden by terminology.
- Route findings to the owning skill/layer instead of expanding this skill into a universal designer.
- Optimize the readiness report for a normal developer/product owner to make a go/no-go decision.
