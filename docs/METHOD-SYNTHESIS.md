# Design Before Code — Method Synthesis

This document explains how Design Before Code synthesizes ideas from established open-source agent workflows and design methods.

The goal is not to copy upstream prompts or to invent an isolated methodology. The project combines proven mechanisms, removes ceremony that does not serve the target user, and adds missing cross-discipline review behavior for greenfield AI-assisted development.

## Synthesis rule

A mechanism may enter a core Skill only when all of the following are true:

1. it solves a recurring problem in pre-implementation design;
2. it can be expressed in domain-general terms;
3. it supports the Project Charter's goal of human-reviewable decisions;
4. it does not force a particular architecture, framework, database, or UI style;
5. it has been rewritten and adapted to this project's workflow rather than copied as a block of prompt text.

When a source mechanism is intentionally not adopted, this document records why.

## Upstream methods

### Superpowers — `brainstorming`

Source: https://github.com/obra/superpowers/tree/main/skills/brainstorming

Mechanisms worth preserving:

- understand the existing project context before designing;
- refine intent before implementation;
- present the design before invoking implementation work;
- require explicit human approval before code/scaffolding begins;
- decompose oversized requests rather than pretending one spec can safely cover unrelated subsystems;
- use design as a collaborative, correctable artifact rather than an agent monologue.

How Design Before Code adapts them:

- the human approval hard gate is shared by all four core Skills;
- oversized-domain decomposition belongs primarily in `business-domain-design`;
- approval is not one final generic design approval: business, UX, data, and cross-artifact readiness remain separately reviewable;
- the project does not inherit Superpowers' full implementation workflow because OpenSpec or another orchestrator may own planning/implementation later.

### BMad Method — planning, UX, implementation readiness

Source: https://github.com/bmad-code-org/BMAD-METHOD

Mechanisms worth preserving:

- separate planning artifacts from implementation;
- scale planning depth according to problem complexity;
- treat UX behavior/experience as an explicit planning contract rather than something discovered in frontend code;
- model named user journeys and information architecture;
- enumerate surface states such as empty/loading/error/permission/conflict when applicable;
- validate planning artifacts for coverage and cohesion before implementation;
- keep behavioral UX concerns separable from visual identity/design-system concerns.

How Design Before Code adapts them:

- `ux-flow-design` is intentionally closer to BMad's behavioral `EXPERIENCE` spine than to a visual `DESIGN` spine;
- visual branding/tokens are excluded from the current project because the original user problem is task-flow opacity, not brand-system generation;
- `design-readiness-review` borrows the readiness/cohesion idea but checks Business ↔ UX ↔ Data rather than PRD ↔ Architecture ↔ Stories;
- reviewer coverage is made compact and cheap by default because this project explicitly optimizes evaluation/token cost.

Trademark note: BMad/BMAD Method are upstream trademarks. Design Before Code is an independent project and should not be branded as a BMad product or official extension.

### GitHub Spec Kit

Source: https://github.com/github/spec-kit

Mechanisms worth preserving:

- specification before implementation;
- explicit staged artifacts rather than one giant prose answer;
- clarification markers for unresolved information;
- gate checks before moving into later phases;
- a dedicated data-model artifact with entities, relationships, validation rules, and relevant state transitions;
- runnable/realistic validation scenarios (`quickstart` style) that prove the design works end-to-end;
- re-check gates after design, not only before it.

How Design Before Code adapts them:

- artifact staging becomes Business → UX → Data → Readiness instead of Spec → Plan → Research → Data/Contracts → Tasks;
- `DECISION REQUIRED` + BLOCKED physical/interaction elements serve the role of explicit unresolved clarification;
- scenario simulation is elevated into every relevant design Skill, not only a final implementation quickstart;
- the future OpenSpec integration will borrow the dependency/gate pattern without making Spec Kit or OpenSpec a runtime requirement for the Skills themselves.

### DDD Agent Skill — domain discovery and DDD worthiness

Source: https://github.com/aristorinjuang/ddd-agent-skill

Mechanisms worth preserving:

- decide whether DDD is warranted before applying DDD ceremony;
- distinguish greenfield/brownfield context;
- use collaborative modeling rather than code scaffolding as the default;
- Event Storming as an event-first discovery technique;
- ubiquitous language and ambiguity negotiation;
- bounded contexts only when domain complexity and language/ownership boundaries justify them;
- make domain artifacts living documents that humans can correct.

How Design Before Code adapts them:

- `business-domain-design` uses a three-level modeling-depth gate: Plain CRUD / Light Domain Modeling / Deep Domain Discovery;
- Event Storming is used as a lightweight discovery lens when behavior is complex, not as a mandatory artifact for every feature;
- ubiquitous language becomes the business terminology/glossary step;
- bounded-context analysis is optional and only appears when different capabilities, ownership, or meanings genuinely conflict;
- tactical DDD (aggregates, repositories, factories) and DDD code generation are intentionally excluded because this project's first goal is pre-implementation product clarity, not DDD implementation.

### softaworks `database-schema-designer`

Source: https://github.com/softaworks/agent-toolkit/tree/main/skills/database-schema-designer

Mechanisms worth preserving:

- domain modeling before UI-shaped tables;
- normalize to a clear baseline before intentional denormalization;
- database constraints are part of the model;
- indexes should be justified by access patterns;
- SQL/NoSQL choice should follow workload and data semantics rather than fashion;
- migration/evolution concerns matter for mature schemas.

How Design Before Code adapts them:

- `data-model-design` adds a business-readable conceptual/logical layer before physical schema output;
- normalization is combined with explicit transitive-dependency and redundancy review;
- access-pattern-based indexing is retained;
- database integrity is strengthened with declarative-constraint review (including composite constraints where appropriate);
- migration generation is excluded from the greenfield design Skill and belongs later in implementation/brownfield workflows.

## Cross-source synthesis by Skill

| Design Before Code Skill | Primary upstream strengths synthesized | Distinctive adaptation in this project |
|---|---|---|
| `business-domain-design` | Superpowers discovery/approval; DDD worthiness + Event Storming + ubiquitous language; BMad complexity-sensitive planning | domain model without forcing DDD; explicit provenance; downstream UX/Data constraints |
| `ux-flow-design` | BMad EXPERIENCE behavioral spine, journeys, IA, state coverage; Superpowers review-before-implementation | enterprise task-first UX, low-fi flows, failure/recovery, no visual-design-system requirement |
| `data-model-design` | Spec Kit explicit data-model artifact + validation; schema-designer normalization/constraints/access patterns; DDD lifecycle semantics | temporal business-time reasoning, scenario simulation, transitive redundancy, human decision locks |
| `design-readiness-review` | BMad readiness/cohesion validation; Spec Kit gates; Superpowers human approval | cross-artifact Business ↔ UX ↔ Data contradiction detection and routing back to the owning Skill |

## What is original to Design Before Code

The project does not claim that its ingredients are new. Its original contribution is the way they are combined around a specific failure mode of AI coding agents:

> consequential product, UX, and data decisions are often made implicitly during implementation and become visible to a normal developer only after the MVP exists.

Distinctive project mechanisms include:

- one shared decision-provenance vocabulary across Business, UX, and Data;
- cross-artifact reconciliation of `DECISION REQUIRED` items;
- treating historical semantics as a human-reviewable product decision rather than only a database technique;
- adversarial scenario simulation before implementation;
- routing readiness failures back to the owning design layer instead of letting a final reviewer invent product semantics;
- an evaluation protocol designed to avoid expensive repeated full A/B sessions;
- a Project Charter that prevents benchmark-specific prompt drift.

## Anti-copy rule

Prefer conceptual synthesis and rewritten instructions.

If future contributors copy substantial upstream text, templates, or code rather than independently rewriting/adapting the mechanism, they must:

1. identify the copied material explicitly;
2. preserve the upstream copyright/license notice as required;
3. update `ATTRIBUTION.md`;
4. avoid upstream trademarks or branding in a way that implies affiliation.
