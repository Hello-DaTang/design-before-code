# ux-flow-design

Design and review user task flows before frontend implementation. Optimize for task clarity, low friction, recoverability, and human reviewability before visual polish or component selection.

**Current behavior target: v0.2.**

This Skill synthesizes BMad-style behavioral UX planning (journeys, information architecture, state coverage, reviewer closure) with Superpowers-style design-before-implementation gating. It deliberately excludes visual-brand-system work from the core flow.

## When to use

Use this skill after the business model is sufficiently understood and before frontend/page implementation when the user journey, information architecture, or page states are not yet trusted.

It is especially useful for enterprise CRUD/admin systems where agents tend to mirror backend entities into menus, pages, dialogs, and forms without validating whether that workflow is natural for users.

Do not use this skill as a visual-branding system, design-token generator, high-fidelity UI generator, CSS framework chooser, or frontend code generator.

## Non-negotiable rules

1. **Do not derive UX from routes, database tables, DTOs, controllers, or existing component structure.**
2. **Do not treat one backend entity as automatically requiring one menu item, one list page, and one edit dialog.**
3. **Do not optimize for fewer clicks in isolation; optimize for fewer unnecessary decisions, lower cognitive load, and clear task completion.**
4. **Do not design only the happy path. Relevant loading, empty, validation, permission, conflict, failure, offline, and recovery states are part of the UX contract.**
5. **Do not hide consequential product decisions inside interaction details. Mark unresolved behavior as DECISION REQUIRED.**
6. **Do not use visual polish to compensate for an unclear task flow. Behavior comes before styling.**
7. **Do not force desktop-SaaS patterns, wizard patterns, modal dialogs, tabs, or tables unless they fit the user task.**
8. **Do not begin frontend implementation before the behavioral UX artifact has been presented for human review.**

## Behavioral UX contract

The primary output of this Skill is a **behavioral contract**: how the product works for people.

It owns:

- user journeys;
- information architecture;
- surface responsibilities;
- interaction primitives;
- state and recovery behavior;
- permissions and consequence visibility;
- accessibility floor;
- downstream capability implications.

It does **not** own brand identity, color palette, typography system, spacing tokens, illustration style, or high-fidelity visual polish. Those can be produced by a separate visual-design workflow later.

## Required output sequence

1. Context and decision provenance
2. Actors, jobs, and task priorities
3. Information architecture
4. Named-protagonist journeys / key flows
5. Surface inventory and responsibilities
6. Interaction and state model
7. Exception and recovery flows
8. Low-fidelity wireframes
9. Accessibility floor and surface-closure review
10. Downstream implications and human review gate

## 1. Context and decision provenance

Summarize the UX problem and classify consequential statements as:

- **FACT** — explicit requirement or established business rule.
- **INFERENCE** — a conclusion logically derived from facts.
- **RECOMMENDATION** — an interaction/design proposal.
- **ASSUMPTION** — a provisional choice used to continue design.
- **DECISION REQUIRED** — a human/product choice that materially changes task flow, permissions, state behavior, or information architecture.

Existing menus/pages/routes are implementation context, not automatically the desired UX.

If upstream `business-domain-design` contains unresolved lifecycle/ownership decisions that materially change the user flow, keep the affected UX element blocked rather than silently choosing one branch.

## 2. Actors, jobs, and task priorities

For each actor identify:

- their business goal;
- the task they are trying to complete;
- task frequency;
- task urgency/risk;
- information needed before acting;
- decisions they must make;
- actions they are allowed to perform;
- what success looks like.

Prefer task language such as:

- "record today's production result";
- "review an exception and decide whether to approve";
- "find a historical order and understand why it changed";

over page language such as "open ProductionRecord.vue".

Classify tasks roughly as primary, secondary, or rare/administrative when useful.

## 3. Information architecture

Organize information around user goals and business concepts, not backend modules.

For each proposed navigation area or entry point explain:

- which user task it supports;
- why it deserves a top-level entry versus being contextual;
- which concepts belong together from the user's perspective;
- what should be reachable from context instead of duplicated in navigation.

Challenge menu sprawl.

Example: Product Version may deserve a contextual sub-view inside Product rather than a separate top-level menu if users never manage versions independently.

Do not finalize navigation if the business-domain model still contains unresolved concept boundaries.

## 4. Named-protagonist journeys / key flows

For each high-value task, describe the journey around a **named protagonist or explicit role** rather than an abstract screen sequence.

Example:

```text
Li, Production Operator
Goal: record today's completed production accurately
Entry: Today's Production workspace
  ↓
Select/confirm business context
  ↓
Review effective version/department context
  ↓
Enter actual result
  ↓
Validate consequential rules
  ↓
Submit
  ↓
See confirmation + next useful action
```

For each key flow include:

- protagonist/role;
- goal;
- entry point(s);
- numbered happy-path steps;
- required context before each consequential action;
- decision branches;
- success/climax moment;
- likely next action;
- at least one failure/recovery path when applicable;
- drop-off/friction risks for long or high-frequency flows.

Challenge unnecessary page hops, forced context switching, duplicate data entry, and actions placed far from the evidence needed to decide them.

## 5. Surface inventory and responsibilities

Only after journeys are understandable, propose pages, panels, drawers, dialogs, or embedded sections.

Create a compact **surface inventory**. For each surface answer:

- **Why this surface exists**
- which journey/task uses it;
- what information must be visible before acting;
- what the dominant primary action is;
- what belongs elsewhere;
- whether the surface is persistent navigation, contextual detail, transient interaction, or confirmation;
- which states must be supported.

A surface should not exist merely because there is a backend table.

### Page versus dialog/drawer decision

Prefer a dedicated page when the task:

- has meaningful navigation/history value;
- requires substantial context;
- contains multiple sections or subflows;
- may be bookmarked/shared/revisited;
- deserves browser back/forward behavior.

Prefer a dialog/drawer only when the action is tightly contextual, small, and does not require a separate navigation state.

Do not use nested dialogs for complex business tasks.

## 6. Interaction and state model

For every important surface/action review applicable states:

- default/ready;
- cold loading;
- empty;
- success;
- validation error;
- server/network failure;
- offline/degraded state when relevant;
- permission denied/read-only;
- stale/conflicting update;
- disabled/unavailable action;
- partially completed work/draft, if supported;
- destructive/consequential confirmation.

State design must answer:

- what the user sees;
- what they can do next;
- whether entered work is preserved;
- how they recover;
- whether the system explains why an action is unavailable.

Do not use a generic toast as the only recovery design for consequential failures.

## 7. Exception and recovery flows

For each important journey include at least one realistic exception when relevant.

Examples:

- duplicate submission;
- record changed by someone else;
- object became disabled after the page loaded;
- permission changed;
- partial operation succeeded;
- user tries to cancel after downstream work has begun;
- validation depends on server-side business rules;
- a historical/backdated entry requires different defaults than a current entry.

The flow should explain recovery, not merely name the error.

If exception behavior depends on unresolved business policy, mark **DECISION REQUIRED**.

## 8. Low-fidelity wireframes

Use simple ASCII, Markdown, or Mermaid sketches to expose hierarchy and interaction before visual styling.

Focus on:

- information hierarchy;
- placement of primary and secondary actions;
- contextual relationships;
- list/detail/edit transitions;
- where warnings, status, and history appear;
- whether a user can understand what to do next.

Do not spend effort on color, spacing tokens, icon libraries, or polished visual design unless those affect comprehension.

## 9. Accessibility floor and surface-closure review

### Accessibility floor

At planning time, check only the behavioral/accessibility basics relevant to the flow:

- keyboard-reachable critical actions for web/desktop surfaces;
- visible focus/interaction order expectations;
- labels/instructions not conveyed by color alone;
- error messages associated with the action/input they explain;
- status changes announced or otherwise perceivable when needed;
- touch targets and mobile constraints when mobile is in scope.

This is an accessibility floor, not a full visual WCAG audit.

### Surface closure

Walk every surface in the IA/surface inventory and verify that applicable states are actually specified.

For each surface ask:

- which key flow reaches it?
- what is its default state?
- can it be empty?
- can loading fail?
- can permission be missing?
- can data become stale/conflicting?
- what does recovery look like?

A flow is not complete if the journey diagram names a surface whose critical state behavior is unspecified.

Then run the broader friction audit:

- menu items created from backend entity names;
- unnecessary page transitions;
- duplicate entry of information the system already knows;
- actions separated from required decision context;
- multiple equal-looking primary actions;
- hidden destructive/consequential effects;
- ambiguous business terminology;
- modal/dialog overuse;
- flows that work only in ideal sequence;
- current-state defaults that become wrong for historical/backdated work;
- admin-heavy design imposed on high-frequency operational users;
- speculative screens for future requirements;
- information displayed because it exists in the database rather than because it helps the task.

Read `references/ux-flow-principles.md` for deeper guidance.

## 10. Downstream implications and human review gate

Do not write frontend code.

List implications that downstream implementation and data design must respect, for example:

- Data model must support drafts because the UX allows save-and-resume.
- API must support optimistic conflict detection because the UX promises recoverable concurrent editing.
- Historical entry must resolve version/department at business-effective time because the UX allows backdated entry.
- Permission model must distinguish view and approve because those are separate actions in the flow.

End with exactly one result:

- **Ready for implementation design** — main task flows and critical states are coherent.
- **Needs human decision** — list unresolved interaction/product choices and what they block.
- **Not ready** — upstream business semantics are too incomplete or contradictory to design a stable UX.

Present the behavioral UX artifact for human correction/approval before frontend implementation. Do not self-approve consequential workflow decisions.

## Design principles

- Start from user goals and business tasks, not frontend files.
- Behavior before appearance.
- A named human/role journey is usually easier to review than an abstract page graph.
- Contextual actions should stay near the evidence that makes them understandable.
- The most frequent/important task should have the clearest path, not necessarily the shortest click count.
- Progressive disclosure is preferable to showing every field and action at once.
- Destructive, irreversible, financial, approval, or historical actions deserve explicit consequence visibility.
- Empty/error/permission/conflict states are part of the product, not implementation leftovers.
- Every IA surface should close over the states its journeys require.
- Different actors may need different task flows over the same business concepts.
- Avoid speculative pages and generic dashboards without a concrete user job.
- Optimize artifacts so a normal developer or product owner can review the flow without being a UX specialist.
