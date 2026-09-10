# Cross-Artifact Review Principles

Use these principles when `design-readiness-review` needs deeper reasoning.

## 1. Review relationships, not document quality in isolation

A polished business model, UX flow, and schema can still describe three different systems.

The review target is semantic alignment across artifacts.

## 2. Reconcile decisions before comparing details

Start with decisions that materially affect lifecycle, history, ownership, permissions, cardinality, or user behavior.

If one artifact still marks a question unresolved and another silently selected an answer, that is a drift defect even if the selected answer is reasonable.

## 3. Trace primary tasks end to end

For each important user task, identify:

- the business goal/rule;
- the UX entry/action/state transition;
- the data/history/API support needed.

A missing link is more important than an extra diagram.

## 4. Business lifecycle is the authority for allowed transitions

UX may expose an action only if the business model permits it.

Data may encode a state only if it represents a real business or justified technical state.

Do not let implementation convenience define the domain lifecycle.

## 5. UX promises create contracts

Features such as draft, undo, conflict recovery, partial completion, historical edit, and retry are not frontend-only details.

They imply persistence, state, history, idempotency, concurrency, or API behavior.

The readiness review should name that contract explicitly.

## 6. Data constraints are product behavior

A UNIQUE constraint, cascade delete, non-null field, version lock, or immutable row can affect what users are allowed to do.

Verify that these constraints match approved business semantics and UX behavior.

## 7. Technical soft delete is not automatically business cancellation

If the user performs “Cancel”, “Void”, “Reject”, or “Withdraw”, confirm whether the business concept needs an explicit state/event/history rather than only a framework `deleted` flag.

## 8. Historical semantics must agree across all views

If business says past transactions preserve the applied value, UX should display/explain that historical value and data must preserve it.

If business says historical reports intentionally recompute using latest policy, data/UX should not pretend the old applied value is frozen.

## 9. Same words can hide different semantics

Terms such as `current`, `delete`, `version`, `status`, `department`, `owner`, `price`, and `plan` frequently drift across artifacts.

Compare definitions, not just names.

## 10. Scenario matrices beat static checklists

A realistic timeline exposes disagreements that static artifact review misses.

Strong scenarios include a later mutation or exception and verify what the user sees, what the business says is true, and what data can reconstruct.

## 11. Severity is about implementation risk

BLOCKER means implementation would require guessing consequential semantics.

MAJOR means a meaningful flow/state/history path is unsupported or incomplete, but the conceptual direction is stable.

MINOR means clarification is useful but does not materially alter product behavior or core model structure.

## 12. Route defects to the owning layer

Do not fix business semantics inside the readiness reviewer.

Examples:

- unclear cancellation policy → business-domain-design;
- awkward/missing recovery interaction → ux-flow-design;
- missing historical representation → data-model-design.

The readiness review is a gate and router, not a universal design layer.

## 13. READY is deliberately strict

READY means implementation does not need to invent major product semantics.

It does not mean every label, microcopy, index, visual style, or implementation detail is finished.

## 14. Human approval is distinct from agent readiness

An agent can conclude the artifacts are internally coherent, but consequential product decisions still belong to humans.

Do not equate self-consistency with stakeholder approval.
