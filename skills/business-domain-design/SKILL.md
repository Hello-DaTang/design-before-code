# business-domain-design

Turn rough requirements, meeting notes, stakeholder explanations, and partial product understanding into a reviewable business model before UX, database, API, or implementation design.

**Current behavior target: v0.2.**

This Skill synthesizes collaborative design-gating ideas from Superpowers, complexity-sensitive planning from BMad, and selected discovery techniques from Domain-Driven Design. It uses those techniques only when they clarify the business problem; it does not force DDD ceremony.

## When to use

Use this skill for greenfield / 0→1 features when the business model is not yet explicit enough for downstream UX or data design.

Use it before `ux-flow-design` and `data-model-design` when meeting notes still mix business facts, screen ideas, implementation ideas, and unresolved policy.

Do not use it as a full DDD transformation, architecture generator, bounded-context generator, or code-scaffolding workflow.

## Non-negotiable rules

1. **Do not jump from rough requirements to code, database tables, API endpoints, classes, services, or pages.**
2. **Choose modeling depth before applying modeling ceremony. Plain CRUD is a valid outcome.**
3. **Do not treat a screen, form, table, API payload, or existing class as proof that a business concept exists.**
4. **Do not silently turn recommendations or plausible assumptions into business facts.**
5. **Do not collapse concepts with materially different lifecycles just because they appear on the same screen.**
6. **Do not split one simple concept into multiple entities merely to look architecturally sophisticated.**
7. **Do not resolve consequential product/business ambiguity without making the choice visible to the human reviewer.**
8. **Do not hand off to UX, data, planning, or implementation until the business model has been presented for human review.**

## Required output sequence

1. Context and decision provenance
2. Modeling-depth gate
3. Actors and goals
4. Business terminology / ubiquitous language
5. Business discovery and key events
6. Business concepts and responsibilities
7. Lifecycles, states, and rules
8. Normal and exceptional scenarios
9. Model challenges and optional boundaries
10. Downstream implications and human review gate

## 1. Context and decision provenance

Summarize the business goal and separate consequential statements into:

- **FACT** — explicitly stated by the requester/stakeholder/source.
- **INFERENCE** — logically derived from facts.
- **RECOMMENDATION** — a design suggestion from the agent.
- **ASSUMPTION** — a provisional choice used only to continue analysis.
- **DECISION REQUIRED** — a human/business choice that materially changes lifecycle, ownership, policy, user flow, or downstream data design.

Framework conventions and existing code are context, not automatically business truth.

If the request covers several independent capabilities, identify the decomposition before pretending one business model safely covers all of them.

## 2. Modeling-depth gate

Before using DDD-style artifacts, classify the scoped problem.

### Plain CRUD

Use when the capability is mostly stable master-data maintenance or straightforward create/read/update/deactivate behavior with little behavioral complexity.

Expected modeling:

- actors/goals;
- terminology;
- core concept(s);
- simple lifecycle/rules;
- scenarios and unresolved decisions.

Do **not** invent domain events, aggregates, bounded contexts, or strategic maps just to fill a template.

### Light Domain Modeling

Use when several concepts interact, lifecycles differ, rules/exceptions matter, or history/ownership causes non-trivial behavior.

Add:

- key business events;
- lifecycle/state reasoning;
- explicit rules/invariants;
- event-first scenario discovery;
- concept split/merge challenges.

### Deep Domain Discovery

Use only when the scope contains substantial behavioral complexity, strategic value, volatile policy, conflicting terminology, multiple ownership boundaries, or genuinely different business capabilities.

Then selectively use:

- Event Storming-style discovery;
- ubiquitous-language negotiation;
- subdomain/capability boundaries;
- bounded-context candidates and context relationships.

Even here, this Skill stops at business modeling. Tactical DDD implementation concepts such as repositories/factories/aggregate code belong elsewhere.

Record the selected depth and why.

## 3. Actors and goals

For each actor identify:

- who they are in business terms;
- what outcome they are trying to achieve;
- what they are responsible for;
- what they are allowed or not allowed to decide;
- what other actors they depend on.

Prefer business roles such as `Production Planner`, `Warehouse Operator`, or `Customer` over implementation roles such as `AdminControllerUser`.

## 4. Business terminology / ubiquitous language

Create a compact glossary for important terms.

For each term capture:

- plain-language meaning;
- whether it has identity over time;
- what it must not be confused with;
- aliases/synonyms from the source material when relevant.

If stakeholders use one word for different concepts, or different words for the same concept, surface the ambiguity and negotiate the model instead of silently selecting terminology.

Use domain language consistently across later artifacts. Do not require the human reviewer to understand DDD jargon.

## 5. Business discovery and key events

For Light or Deep modeling, use an event-first discovery pass before fixing structure.

Ask what **happens** in the business, then work backward/forward:

```text
Trigger / intent
   ↓
Command or decision by an actor
   ↓
Business event that becomes true
   ↓
Rules/policies that permit or reject it
   ↓
Affected business concepts / follow-up events
```

Examples:

- Customer submits order → OrderPlaced.
- Payment provider confirms funds → PaymentSucceeded.
- Supervisor approves plan → PlanApproved.
- Product definition becomes effective → VersionActivated.

This is Event Storming-lite: enough to reveal hidden concepts, responsibilities, and exception paths without forcing a full workshop artifact.

For each important event ask:

- who/what causes it;
- what must already be true;
- what becomes true afterward;
- what can fail or be rejected;
- which concept owns the rule;
- whether another lifecycle begins because of it.

Events are discovery tools, not a requirement to implement event sourcing.

## 6. Business concepts and responsibilities

Identify the smallest set of durable business concepts needed to explain the requirements.

For each concept answer:

- **Why this exists**
- what it represents in the real world;
- what responsibility belongs to it;
- whether it has its own identity;
- who owns or controls it;
- what other concepts it relates to;
- what does **not** belong to it.

Challenge both directions:

- **over-modeling** — two concepts are really one thing with one lifecycle;
- **under-modeling** — one concept hides two things with different lifecycles, ownership, timing, or rules.

Strong split signals include:

- plan vs actual execution;
- order vs payment;
- order vs shipment;
- request vs approval decision;
- current definition vs historical event;
- product identity vs product version.

Do not decide table or page boundaries yet.

## 7. Lifecycles, states, and rules

For every consequential concept ask:

- when does it come into existence?
- who can create it?
- what meaningful states can it enter?
- what events cause transitions?
- can it be corrected, cancelled, superseded, archived, or reopened?
- when is it considered complete/final?
- can later events change how older events should be interpreted?

Separate **state** from **event**.

Example:

- `Order is Paid` is a state/result.
- `PaymentSucceeded` is an event that may cause that result.

Extract business rules separately from implementation mechanisms. Classify each important rule as explicitly required, inferred, recommended, or unresolved.

Do not invent a detailed state machine when the source only supports a simple lifecycle.

## 8. Normal and exceptional scenarios

Run at least one realistic normal scenario and one exception/edge scenario through the business model.

Use concrete actors, dates, states, quantities, or decisions when useful.

When relevant include:

- late entry;
- cancellation after partial progress;
- correction after approval;
- reassignment;
- version/policy change;
- partial fulfillment;
- repeated attempt/retry;
- conflicting actors.

If the scenario requires a concept, event, or rule that the model does not contain, revise the model or mark a decision required.

## 9. Model challenges and optional boundaries

Before declaring the business model ready, explicitly challenge:

- concepts derived only from screens or code structure;
- concepts with no independent lifecycle or responsibility;
- two concepts that have different lifecycles but were merged;
- duplicated terms with conflicting meanings;
- generic `status/type/config/record` nouns that hide business meaning;
- assumptions presented as rules;
- speculative future features driving current complexity;
- important ownership or responsibility that is unclear;
- missing exception/recovery behavior;
- business events that cannot be explained by the proposed model.

### Optional capability / bounded-context analysis

Only for Deep Domain Discovery, ask whether different parts of the model have:

- different business language for similar words;
- different owners/teams or policies;
- independent lifecycles and decision authority;
- integration relationships rather than one coherent model.

If so, propose **candidate** capability/bounded-context boundaries with reasons. Do not equate bounded context automatically with microservice or deployment unit.

Read `references/business-modeling-principles.md` for deeper guidance.

## 10. Downstream implications and human review gate

Do not design UX or database structures here.

Instead, list implications that downstream skills must respect.

Examples:

- UX must show that Plan exists before Actual execution.
- Data design must preserve which Version applied to a historical event.
- UX must support cancellation after partial fulfillment.
- Data model must not assume one Payment if multi-payment remains DECISION REQUIRED.

End with exactly one readiness result:

- **Ready for downstream design** — the business model is coherent and no material semantic ambiguity remains.
- **Needs human decision** — list unresolved choices and what downstream design they block.
- **Not ready** — the source material is too incomplete, contradictory, or oversized to establish a stable business model.

Present the business model for human correction/approval before any downstream implementation work begins. Do not self-approve a consequential business decision.

## Design principles

- Model business reality before UI, storage, or code structure.
- Choose modeling depth to fit complexity; saying “Plain CRUD is enough” is a successful outcome.
- Use DDD techniques as discovery tools, not mandatory ceremony.
- Prefer concrete business language over architecture jargon.
- Discover behavior through events before freezing structure when behavior is complex.
- Different lifecycle, ownership, timing, or rules are strong signals that concepts may be separate.
- Shared screen placement is not evidence of shared identity or lifecycle.
- A concept without identity or independent lifecycle may be an attribute/value rather than an entity-like concept.
- Simplicity is a constraint: every additional concept or boundary must earn its place.
- Keep unresolved semantics visible for UX and data-model design instead of hiding them in implementation choices.
