# UX Flow Principles

Use these principles when `ux-flow-design` needs deeper reasoning.

## 1. Start from the job, not the screen

Ask what the user is trying to accomplish before naming pages or components.

A backend concept does not automatically imply a standalone screen. The same business concept may appear in multiple task contexts, and several backend concepts may belong on one coherent task surface.

## 2. Frequency, risk, and context shape the flow

A high-frequency operational task should minimize repeated decisions and unnecessary navigation.

A rare but high-risk task may intentionally require more explicit review and confirmation.

Do not optimize all flows for raw click count.

## 3. One dominant next action when appropriate

A screen should usually make the user's likely next action obvious.

Secondary actions should remain available without competing visually or conceptually with the primary task.

If several actions are equally important, explain why rather than creating accidental ambiguity.

## 4. Keep decisions near their evidence

An action should be placed where the user can see the information needed to make that decision.

Examples:

- approve/reject near the request details and risk information;
- version switch near version history and effective date;
- refund near paid/refundable amounts and line-item context.

Do not force users to remember context from a previous page.

## 5. Progressive disclosure

Show what is necessary for the current decision first. Reveal advanced configuration, history, or secondary metadata when needed.

Do not turn every database column into a visible form field.

## 6. Page, drawer, and dialog are different navigation commitments

A dialog is not a cheap substitute for a page.

Use transient surfaces for small contextual actions. Use persistent pages for workflows with meaningful navigation, substantial context, history, or multi-step work.

Nested dialogs are a warning sign that the task probably deserves a clearer structure.

## 7. State is part of UX

A design is incomplete if it only describes the populated success state.

Review relevant states:

- loading;
- empty;
- read-only/permission denied;
- validation failure;
- server/network error;
- stale/conflicting data;
- disabled action;
- partial completion/draft;
- completed/closed/archived.

Every error state should answer: what happened, what was preserved, and what can the user do next?

## 8. Historical/backdated work needs historical context

If users can enter or correct past business events, current defaults may be wrong.

Examples:

- current department may differ from department at event time;
- current product version may differ from the historical version;
- current price/rate/standard may not apply to the backdated event.

The UX should make the effective date/context visible and avoid silently defaulting to current state when that could create incorrect history.

## 9. Conflict is not just an error toast

When concurrent edits matter, define:

- how the user learns the record changed;
- whether their work is preserved;
- whether they can compare/reload/reapply changes;
- whether last-write-wins is acceptable.

If the product promises recoverability, downstream APIs/data models must support it.

## 10. Destructive and consequential actions expose consequences

Delete, cancel, approve, reject, refund, publish, close, and version-switch actions may affect downstream work.

The UI should explain meaningful consequences before committing them. Confirmation text should be specific to the business effect, not a generic “Are you sure?”.

## 11. Different actors may need different surfaces

Do not create one giant admin page if operational users, reviewers, managers, and maintainers have different tasks.

Different views over the same business concepts are acceptable when they serve genuinely different jobs.

## 12. Avoid menu sprawl

Top-level navigation is expensive cognitive real estate.

A concept deserves a dedicated entry when users intentionally navigate to it as a task destination. Otherwise, prefer contextual access.

## 13. Tables are not the default answer

Tables are useful for scanning, comparing, sorting, and bulk operations. They are poor default surfaces for tasks requiring narrative context, complex state, or guided decisions.

Choose a table because the user's task benefits from tabular comparison, not because the backend returns a list.

## 14. Low fidelity first

Before colors, components, spacing, icons, or design systems, validate:

- hierarchy;
- task sequence;
- information grouping;
- action placement;
- state transitions;
- exception recovery.

A clean wireframe can reveal workflow problems earlier and more cheaply than polished implementation.

## 15. UX decisions create downstream constraints

A UX promise can imply technical requirements.

Examples:

- save draft → persistent draft state;
- undo/correction history → audit/history model;
- partial fulfillment → independent fulfillment lifecycle;
- backdated entry → business-effective-time resolution;
- recoverable concurrent edit → conflict/version support.

Make these implications explicit instead of allowing implementation to discover them later.
