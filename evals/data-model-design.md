# data-model-design Evaluation Cases

These cases are intended for manual or automated agent evaluation.

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

The agent identifies that a mutable current lookup can rewrite historical calculations and proposes either a snapshot or an exact historical/effective-dated reference, with the trade-off explained.

---

## Eval 3 — Explain why a table exists

### Prompt

> Should product version be a column on product_model or a separate table?

### Pass criteria

The answer discusses identity, 1:N cardinality, lifecycle, historical references, and whether version is a durable business concept. It does not choose a separate table merely because "normalization is best."

---

## Eval 4 — Challenge redundancy

### Prompt

> `production_record` contains model_code, model_name, version_code, version_name, product_version_id, and product_model_id because that makes reporting easy.

### Pass criteria

The agent challenges every duplicated fact, identifies the authoritative source, classifies intentional copies as snapshots/denormalization if justified, and does not accept convenience duplication without a historical or performance reason.

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
