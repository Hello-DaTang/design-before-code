# Attribution

Design Before Code is an independent project informed by ideas from several open-source specification, agent-skill, UX-planning, and software-design projects.

The project intentionally favors **method synthesis** over prompt copying: we study upstream mechanisms, rewrite them for this project's narrower goal, combine compatible ideas, and record what was adopted or intentionally excluded in [`docs/METHOD-SYNTHESIS.md`](docs/METHOD-SYNTHESIS.md).

## Inspirations

### Superpowers

- Repository: https://github.com/obra/superpowers
- Relevant skill: `skills/brainstorming`
- License: MIT
- Influence: design-before-implementation discipline, collaborative refinement, decomposition of oversized scope, and explicit human approval before implementation.

### GitHub Spec Kit

- Repository: https://github.com/github/spec-kit
- License: MIT
- Influence: staged specification artifacts, explicit clarification/gates, dedicated data-model planning, validation scenarios, and re-checking constraints before implementation.

### BMad Method

- Repository: https://github.com/bmad-code-org/BMAD-METHOD
- License: MIT
- Influence: complexity-sensitive planning, UX behavioral planning, user journeys, information architecture, state coverage, planning-artifact cohesion, and implementation-readiness review.
- Trademark note: BMad™, BMad Method™, and BMad Core™ are upstream trademarks. Design Before Code is independent and must not be represented as an official BMad product, fork, or endorsed extension.

### DDD Agent Skill

- Repository: https://github.com/aristorinjuang/ddd-agent-skill
- Influence: DDD-worthiness gate, collaborative domain discovery, Event Storming, ubiquitous language, and using bounded contexts only when complexity warrants them.
- Design Before Code does not copy the full tactical-DDD/code-generation workflow; it adapts only the discovery mechanisms useful before UX/data design.

### softaworks agent-toolkit / database-schema-designer

- Repository: https://github.com/softaworks/agent-toolkit
- Relevant skill: `skills/database-schema-designer`
- License: MIT
- Influence: domain modeling over UI-shaped tables, normalization, constraints, access-pattern-based indexing, deliberate denormalization, and schema-review discipline.

## How the synthesis differs

Design Before Code combines these influences around a narrower product problem:

> AI coding agents often make consequential business, UX, and data-model decisions implicitly during implementation, while the human reviewer sees those decisions only after an MVP exists.

The project's distinctive combination is:

- a shared decision-provenance vocabulary across Business, UX, and Data;
- complexity-sensitive domain modeling without mandatory DDD ceremony;
- behavioral UX planning separated from visual-brand-system work;
- business-time and historical-semantics review in the data model;
- adversarial scenario validation before implementation;
- a cross-artifact readiness gate that reconciles Business ↔ UX ↔ Data;
- explicit human approval after readiness rather than agent self-approval;
- low-cost regression evaluation and anti-drift governance.

See [`docs/METHOD-SYNTHESIS.md`](docs/METHOD-SYNTHESIS.md) for the detailed mechanism-by-mechanism map.

## Copying policy

No substantial source text is intended to be copied verbatim unless explicitly identified.

When future work copies substantial upstream text, templates, code, or other copyrightable material instead of independently rewriting/adapting the underlying mechanism, contributors must:

1. identify the reused material;
2. preserve the upstream copyright and license notices as required;
3. update this file with the specific source;
4. comply with upstream trademark policies;
5. avoid wording or branding that implies upstream endorsement.
