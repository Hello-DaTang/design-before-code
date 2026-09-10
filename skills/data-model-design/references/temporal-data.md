# Temporal Data Review

Temporal correctness is about preserving the business truth the system is supposed to answer after facts, definitions, or relationships change.

## Two clocks: business time and system time

Before choosing a history strategy, distinguish two different clocks.

### Business-effective time

When the real-world fact was true or the business event actually occurred.

Examples:

- a production run happened on September 1;
- an employee belonged to Department A on September 1;
- a price or standard became effective on September 1.

### System recording time

When the system row was inserted, updated, imported, approved, or corrected.

These times can differ because of late entry, backdated correction, batch import, delayed approval, offline work, or migration of historical data.

A model is temporally wrong if it reconstructs a historical business event using current state at recording time when the business meaning depends on the state at business-effective time.

Example failure:

1. September 1: Zhang belongs to Department A and performs production work.
2. September 5: Zhang transfers to Department B.
3. September 10: the September 1 production record is entered late.

If the application copies Zhang's current department on September 10, the historical production record is incorrectly attributed to Department B.

## First ask why a mutable fact changed

Do not jump from "this can change" to "add a history table." Classify the business semantics first.

### 1. Prospective business change

The old value was correct for the past; a new value becomes correct from a new effective time forward.

Example: a labor standard changes from 20s to 18s starting October 1.

Typical strategies: effective-dated history, a new business version, or a new rate/standard identity.

### 2. Retroactive correction

The old stored value was wrong. The business now says a past fact should be corrected.

Example: a rate was entered as 20s but should always have been 19s from September 1.

A correction may intentionally change how past business truth is interpreted, while still requiring audit history of what the system previously believed.

### 3. Original-applied preservation

A historical event must preserve exactly what was applied or agreed at that event, even if the source definition is later corrected.

Examples: order unit price, signed quotation amount, shipped-to address, a standard value actually used for an approved production calculation.

Typical strategy: value snapshot or exact immutable historical reference.

### 4. Current-value reinterpretation

The business intentionally wants old reports or calculations to use the latest current definition.

This is a valid policy in some domains. If so, a current-value model may be correct. Do not add effective-dated history merely because history exists.

## Mutable-fact decision matrix

For each consequential mutable fact, answer these questions before choosing storage:

| Question | Why it matters |
|---|---|
| Is a change a prospective business change or a correction of the past? | Determines whether old business truth remains valid |
| Should an old event preserve what was originally applied? | Points toward snapshot/immutable reference |
| Should an old event be reinterpreted using the corrected or latest definition? | May justify retroactive correction or current reference |
| Must we answer "what was effective then?" | Requires valid/effective-time semantics |
| Must we answer "what did the system believe then?" | Requires recording/transaction-time history or audit |

Do not collapse these questions into one vague requirement such as "keep history."

## Historical strategies

### Current identity/reference

Keep a stable entity identity and intentionally resolve mutable display attributes or definitions from the current master record.

Use when current-value reinterpretation is intentional or historical meaning is identity-based and current labels are desired.

### Historical relationship reference

Store the identity of the related entity that was associated with the event at business-effective time.

Example: a production record stores `dept_id=10` because Department 10 was responsible when production occurred.

This preserves relationship identity, not a complete value snapshot. If Department 10 is later renamed, a historical screen that joins the current department row will show the new name.

### Value snapshot

Copy the exact value applied at business-effective time so later source changes do not alter the event's original-applied meaning.

Examples:

- order item unit price;
- approved quotation amount;
- shipping address used for a completed shipment;
- standard workhour actually applied to a completed production record, when required by policy;
- department name, only if the historical label itself must remain frozen.

A value snapshot is intentional duplication. Label it as such and identify its source of truth at creation time.

### Effective-dated history

Persist versions or relationships with validity periods such as `effective_from` and `effective_to`, or an equivalent version identity.

Use when the system must reconstruct what definition or relationship was valid at an arbitrary business-effective time.

Examples: product versions, organizational assignment history, contract terms, rate tables, and standards that change prospectively over time.

### Recorded-change / transaction-time history

Preserve when the system learned, stored, or changed a fact.

Use when the system must answer questions such as:

- What did the system believe on September 5?
- When was a retroactive correction entered?
- Which report was produced before the correction?

When both business-valid time and system-belief time matter, the model may need both dimensions. Do not call a single `updated_at` column bitemporal history.

## Review procedure

For every mutable business fact or relationship ask:

1. Can this value or relationship change?
2. Why can it change: prospective change, retroactive correction, or simple label maintenance?
3. What is the business-effective time of the historical event?
4. Can the event be entered or corrected later than that time?
5. If the source changes tomorrow, what should yesterday's event display or calculate with?
6. Does history need the old identity, old label, old scalar value, old full definition, corrected truth, or latest definition?
7. Must the system reconstruct the exact state at an arbitrary business date?
8. Must the system reconstruct what it believed at an earlier system time?
9. Is the requirement legal/audit-critical, reporting-critical, or merely convenient?

Then choose the minimum strategy that answers the required questions. If policy is unresolved, mark it as **DECISION REQUIRED** instead of guessing.

## Common failure modes

### Recording-time state pretending to be event-time truth

A historical event is entered late and the application copies current state instead of the state that applied when the event occurred.

### Mutable reference pretending to be a value snapshot

A historical transaction stores only a foreign key to a mutable reference row. The FK may preserve identity correctly, but it does not freeze mutable labels or attributes.

### Effective-dated history used when business wants latest-value reinterpretation

A designer sees "the value can change" and creates validity periods even though the business explicitly wants old calculations to use the latest current value. This adds complexity while implementing the wrong semantics.

### Correction confused with prospective change

A corrected past fact is modeled as if a new value became effective only from today forward, leaving earlier business truth knowingly wrong.

### Snapshot without a source-of-truth definition

The same field appears in several tables, but nobody knows which copy is authoritative or when the snapshot is taken.

### Version table without version semantics

A table named `*_version` exists, but old versions are updated in place without a defined correction/history policy.

### Audit timestamps mistaken for history

`updated_at` tells when the current row changed. It does not preserve previous values, business-effective periods, or prior system belief.

### Soft delete mistaken for temporal modeling

Keeping a deleted row does not explain which definition or relationship was effective when a historical event occurred.

## Output expectation

Create a concise temporal decision table for consequential mutable facts:

| Fact / relationship | Change semantics | Business-time expectation | Recording-time risk | Strategy | Decision status |
|---|---|---|---|---|---|
| Product version used | Prospective version change | Production keeps version used on production date | Late entry may default to newer version | Historical relationship/version identity | Usually derivable from requirements |
| Employee department | Prospective transfer | Production belongs to responsible department at event time | Late entry can capture current department | Historical relationship reference or assignment history | Depends on ownership policy |
| Standard workhour | Unknown: prospective change vs correction vs latest reinterpretation | Unknown until business decides | Current lookup may rewrite old efficiency | Snapshot / effective-dated / current reference depending on policy | DECISION REQUIRED |

If a stored foreign key preserves only relationship identity while labels remain current, say so explicitly.
