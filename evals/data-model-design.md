# data-model-design Evaluation Cases

These cases are intended for manual or automated agent evaluation.

The goal is not to require one canonical schema. The goal is to test whether consequential design decisions become visible, correctly reasoned about, and left for human approval when business policy is unresolved.

## Eval 1 — No direct requirements-to-SQL jump

### Prompt

> Build a database for product models, versions, standard workhours, and production records. Give me the schema.

### Pass criteria

The agent must not begin with SQL or ORM classes. It first presents conceptual and logical models and explains why the entities exist.

### Fail examples

- Immediately emits `CREATE TABLE` statements.
- Treats form fields as table definitions without business modeling.

---

## Eval 2 — Detect historical rewrite risk

### Prompt

> A production record stores `product_version_id`. Standard workhour is looked up from the version table. The standard can later be corrected. Historical efficiency reports must show the value that was actually used at production time.

### Pass criteria

The agent identifies that a mutable current lookup can rewrite historical calculations and proposes either a value snapshot or an exact historical/effective-dated reference, with the trade-off explained.

---

## Eval 3 — Explain why a table exists

### Prompt

> Should product version be a column on product_model or a separate table?

### Pass criteria

The answer discusses identity, 1:N cardinality, lifecycle, historical references, and whether version is a durable business concept. It does not choose a separate table merely because "normalization is best."

---

## Eval 4 — Challenge direct redundancy

### Prompt

> `production_record` contains model_code, model_name, version_code, version_name, product_version_id, and product_model_id because that makes reporting easy.

### Pass criteria

The agent challenges every duplicated fact, identifies the authoritative source, classifies intentional copies as value snapshots/denormalization/integration copies if justified, and does not accept convenience duplication without a historical or performance reason.

---

## Eval 5 — Surface unknowns instead of guessing

### Prompt

> Our product standard workhour can change sometimes. Design the final tables.

### Pass criteria

The agent explicitly asks or records unresolved policy questions such as whether old production should use the originally applied or corrected value. It marks the design as needing a human decision when that choice materially changes the model.

### Fail examples

- Silently assumes all history must be versioned.
- Silently assumes current values should overwrite history.

---

## Eval 6 — Avoid ritual architecture

### Prompt

> Make the schema enterprise-grade. Add soft delete, audit fields, optimistic locks, JSON extensions, and version tables everywhere.

### Pass criteria

The agent rejects blanket additions and requires a business or technical justification for each mechanism.

---

## Eval 7 — Scenario simulation catches a flawed model

### Prompt

> A01 V1 uses 20s. On September 10 it changes to V2 with 18s. A September 5 production record stores only model_id. Is that enough?

### Pass criteria

The agent simulates the timeline and identifies that `model_id` alone cannot preserve which product version was used. It explains the historical failure in business language before proposing the physical correction.

---

# v0.2 regression cases

These cases came from an A/B evaluation against a strong no-skill baseline. They target failures that a generic "normalize + temporal review" checklist can still miss.

## Eval 8 — Detect transitive / derived redundancy

### Prompt

> `product_version` contains `product_id`. A proposed `production_record` contains both `product_id` and `product_version_id`. The designer says both should be stored because product queries are common.

### Pass criteria

The agent must explicitly notice that `product_id` is derivable through `product_version_id -> product_version.product_id`.

It must then:

- classify the duplicate as intentional denormalization only if a concrete reason exists;
- identify the authoritative source;
- explain the contradiction risk (`product_id=A` while `product_version_id` belongs to B);
- explain how consistency would be enforced if both remain;
- consider the non-redundant alternative.

### Fail examples

- Says "the redundancy is acceptable for convenience" without an integrity strategy.
- Reviews duplicate names/codes but misses the FK dependency chain.
- Claims all redundancy has been reviewed while never discussing this pair.

---

## Eval 9 — Business-effective time vs system recording time

### Prompt

> September 1: Zhang works in Department A and completes a production run. September 5: Zhang transfers to Department B. September 10: an administrator enters the September 1 production record. The production record contains `dept_id`. How should `dept_id` be determined?

### Pass criteria

The agent must distinguish:

- business-effective time: September 1;
- system recording time: September 10.

It must reject blindly copying Zhang's current department on September 10 if historical department reporting is intended to reflect where the production occurred.

It should surface the need for either historical assignment data, explicit department selection/verification, or another mechanism capable of resolving the September 1 relationship.

### Fail examples

- "Store the employee's department at entry time."
- Treats create_time as sufficient to reconstruct the department.

---

## Eval 10 — Reference identity is not a full snapshot

### Prompt

> A production record stores only `dept_id=10`. Department 10 was called "Production Department 1" when the record was created, but is renamed to "Smart Manufacturing Department" next year. The historical report joins the current department table for the name. Is `dept_id` a department snapshot?

### Pass criteria

The agent must say that `dept_id` preserves the historical **relationship identity**, not necessarily a full value snapshot.

It should distinguish at least:

- historical relationship reference (`dept_id`);
- value snapshot (`dept_name_at_event`, if required);
- effective-dated department/organization history, if reconstruction of past organization metadata is required.

### Fail examples

- Calls the FK alone a full snapshot without qualification.
- Assumes the old department name is preserved when it is not.

---

## Eval 11 — Do not embed unresolved decisions in the physical model

### Prompt

> The business says a product has one "current version" at a time and each version has an effective date. They have not decided whether version switching needs historical reconstruction. Design the physical model.

### Pass criteria

The agent must identify materially different representations, such as:

- a current-version pointer / current flag optimized for present state;
- effective-dated activation/history optimized for historical reconstruction.

Until the business chooses the semantics, it must not present `current_version_id` (or another single option) as an already-final physical decision.

It should show alternatives or mark affected schema elements as blocked by the decision.

### Fail examples

- Adds `current_version_id` and labels it final while still saying historical switching semantics are unknown.
- Selects an effective-dated model without exposing the trade-off.

---

## Eval 12 — Unresolved unit cannot be silently finalized

### Prompt

> Standard work time is written in seconds (for example 20s), while actual work time is currently entered in hours (for example 7.8h). The business has not yet chosen the canonical storage unit. Give the physical model and list unresolved decisions.

### Pass criteria

The agent must mark the storage type/unit for affected fields as unresolved or present parallel candidates, for example seconds vs decimal hours.

It must not simultaneously say "unit is undecided" and finalize a field such as `actual_work_hours DECIMAL(6,2)` as if approved.

---

## Eval 13 — Different lifecycle may imply different entity

### Prompt

> The screen records plan quantity and actual quantity together at the end of each shift. Sometimes, however, supervisors create and approve the plan before the shift starts, and actual production is entered after the shift ends. Should `plan_quantity` simply be a field on `production_record`?

### Pass criteria

The agent must analyze lifecycle and identity before answering.

It should recognize that a pre-existing/approved plan may be a separate business concept from the later actual-production fact, while also explaining that a single record can remain appropriate if "plan" is only a contextual number entered together with the actual result.

It must not derive entity boundaries from the current screen layout alone.

---

## Eval 14 — Recommendation is not a requirement

### Prompt

> The requirements say products can be disabled and historical production must remain queryable. The agent proposes that product codes can never be changed or reused and that incorrect production records must always be voided and recreated. Are those rules requirements?

### Pass criteria

The agent must separate:

- what is explicitly required;
- what is inferred for integrity;
- what is a recommended invariant;
- what still requires business confirmation.

Common-sense design recommendations must not be relabeled as requirements without evidence.
