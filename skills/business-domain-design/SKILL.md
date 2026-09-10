# business-domain-design

Turn rough requirements, meeting notes, stakeholder explanations, and partial product understanding into a reviewable business model before UX, database, API, or implementation design.

**Current behavior target: v0.1.**

## When to use

Use this skill for greenfield / 0→1 features when the business model is not yet explicit enough for downstream UX or data design.

Use it before `ux-flow-design` and `data-model-design` when meeting notes still mix business facts, screen ideas, implementation ideas, and unresolved policy.

Do not use it as a full DDD transformation, architecture generator, or code-scaffolding workflow.

## Non-negotiable rules

1. **Do not jump from rough requirements to code, database tables, API endpoints, classes, services, or pages.**
2. **Do not force DDD jargon or artifacts onto a simple CRUD domain unless the complexity justifies them.**
3. **Do not treat a screen, form, table, API payload, or existing class as proof that a business concept exists.**
4. **Do not silently turn recommendations or plausible assumptions into business facts.**
5. **Do not collapse concepts with materially different lifecycles just because they appear on the same screen.**
6. **Do not split one simple concept into multiple entities merely to look architecturally sophisticated.**
7. **Do not resolve consequential product/business ambiguity without making the choice visible to the human reviewer.**

## Required output sequence

1. Context and decision provenance
2. Actors and goals
3. Business terminology
4. Business concepts and responsibilities
5. Lifecycles, states, and events
6. Rules and invariants
7. Normal and exceptional business scenarios
8. Model challenges
9. Downstream design implications
10. Human review gate

## 1. Context and decision provenance

Summarize the business goal and separate consequential statements into:

- **FACT** — explicitly stated by the requester/stakeholder/source.
- **INFERENCE** — logically derived from facts.
- **RECOMMENDATION** — a design suggestion from the agent.
- **ASSUMPTION** — a provisional choice used only to continue analysis.
- **DECISION REQUIRED** — a human/business choice that materially changes lifecycle, ownership, policy, user flow, or downstream data design.

Framework conventions and existing code are context, not automatically business truth.

## 2. Actors and goals

For each actor identify:

- who they are in business terms;
- what outcome they are trying to achieve;
- what they are responsible for;
- what they are allowed or not allowed to decide;
- what other actors they depend on.

Prefer business roles such as `Production Planner`, `Warehouse Operator`, or `Customer` over implementation roles such as `AdminControllerUser`.

## 3. Business terminology

Create a compact glossary for important terms.

For each term capture:

- plain-language meaning;
- whether it has identity over time;
- what it must not be confused with;
- aliases/synonyms from the source material when relevant.

Resolve obvious naming collisions. If stakeholders appear to use the same word for different concepts, surface the ambiguity instead of choosing one silently.

Do not require the user to understand `aggregate`, `bounded context`, `entity`, or other DDD terminology unless those concepts genuinely help explain the problem.

## 4. Business concepts and responsibilities

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
- **under-modeling** — one concept actually hides two things with different lifecycles, ownership, timing, or rules.

Examples of lifecycle splits to challenge:

- plan vs actual execution;
- order vs payment;
- order vs shipment;
- request vs approval;
- current definition vs historical event;
- product identity vs product version.

Do not decide entity/table boundaries yet. This stage is business meaning, not storage design.

## 5. Lifecycles, states, and events

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
- `Payment succeeded` is an event that may cause that state.

Do not invent a detailed state machine when the source only supports a simple lifecycle.

## 6. Rules and invariants

Extract business rules separately from implementation mechanisms.

For each important rule classify it as:

- explicitly required;
- inferred;
- recommended;
- unresolved.

Examples:

- one active version per product;
- a refund cannot exceed the refundable amount;
- a completed record cannot be silently rewritten;
- an approval may require a different actor from the requester.

Do not turn common engineering practice into a business invariant without evidence.

## 7. Normal and exceptional business scenarios

Run at least one realistic normal scenario and one exception/edge scenario through the business model.

Use concrete actors, dates, states, quantities, or decisions when useful.

The purpose is to prove the business concepts and lifecycles explain what actually happens, not merely that the glossary sounds reasonable.

When relevant, include changes over time such as:

- late entry;
- cancellation after partial progress;
- correction after approval;
- reassignment;
- version/policy change;
- partial fulfillment;
- repeated attempt;
- retry after failure.

If the scenario requires a concept that the model does not contain, revise the conceptual model or mark a decision required.

## 8. Model challenges

Before declaring the business model ready, explicitly challenge:

- concepts derived only from screens or code structure;
- concepts with no independent lifecycle or responsibility;
- two concepts that have different lifecycles but were merged;
- duplicated terms with conflicting meanings;
- generic `status/type/config` concepts that hide business meaning;
- assumptions presented as rules;
- speculative future features driving current complexity;
- important ownership or responsibility that is unclear;
- missing exception/recovery behavior;
- business events that cannot be explained by the proposed model.

Read `references/business-modeling-principles.md` for deeper guidance.

## 9. Downstream design implications

Do not design UX or database structures here.

Instead, list implications that downstream skills must respect.

Examples:

- UX must show that Plan exists before Actual execution.
- Data design must preserve which Version applied to a historical event.
- UX must support cancellation after partial fulfillment.
- Data model must not assume one Payment if multi-payment remains DECISION REQUIRED.

Mark implications as constraints or blocked decisions, not as final page/table designs.

## 10. Human review gate

End with:

- **Ready for downstream design** — business model is coherent and no material semantic ambiguity remains.
- **Needs human decision** — list unresolved choices and what downstream design they block.
- **Not ready** — the source material is too incomplete or contradictory to establish a stable business model.

Do not self-approve a consequential business decision.

## Design principles

- Model business reality before UI, storage, or code structure.
- Optimize for a normal application developer or product owner to understand and challenge the model.
- Prefer concrete business language over architecture jargon.
- Use DDD ideas as optional reasoning tools, not mandatory ceremony.
- Different lifecycle, ownership, timing, or rules are strong signals that two concepts may be separate.
- Shared screen placement is not evidence of shared identity or lifecycle.
- A concept without identity or independent lifecycle may be an attribute/value rather than an entity-like concept.
- Simplicity is a constraint: every additional concept must earn its place.
- Keep unresolved semantics visible for UX and data-model design instead of hiding them in implementation choices.
