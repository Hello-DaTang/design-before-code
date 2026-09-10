# Temporal Data Review

Temporal correctness is about preserving the business truth that should remain true after mutable reference data changes.

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

These times can differ because of:

- late entry;
- backdated correction;
- batch import;
- delayed approval;
- offline work;
- migration of historical data.

A model is temporally wrong if it reconstructs a historical business event using current state at recording time when the business meaning depends on the state at business-effective time.

Example failure:

1. September 1: Zhang belongs to Department A and performs production work.
2. September 5: Zhang transfers to Department B.
3. September 10: the September 1 production record is entered late.

If the application copies Zhang's **current** department on September 10, the historical production record is incorrectly attributed to Department B.

## Historical strategies

### 1. Current identity reference

Keep a stable entity identity and intentionally resolve mutable display attributes from the current master record.

Use when historical meaning is identity-based and showing the latest display label is acceptable.

Example: a task assigned to employee `E123` may intentionally show that employee's current display name.

### 2. Historical relationship reference

Store the identity of the related entity that was associated with the event at business-effective time.

Example: a production record stores `dept_id=10` because Department 10 was responsible when production occurred.

This preserves the **relationship identity**, but it is not a complete snapshot of the department. If Department 10 is later renamed, a historical screen that joins the current department row will show the new name.

Use this term instead of calling every stored foreign key a "snapshot".

### 3. Value snapshot

Copy the exact value applied at business-effective time so later source changes do not alter historical meaning.

Examples:

- order item unit price;
- approved quotation amount;
- shipping address used for a completed shipment;
- standard workhour applied to a completed production record, when required by the business;
- department name, only if the historical label itself must remain frozen.

A value snapshot is intentional duplication. Label it as such and identify its source of truth at creation time.

### 4. Effective-dated history

Persist versions or relationships with validity periods such as `effective_from` and `effective_to`, or an equivalent version identity.

Use when the system must reconstruct what definition or relationship was valid at an arbitrary business-effective time.

Examples:

- product versions;
- organizational assignment history;
- contract terms;
- rate tables;
- standard definitions that change over time and remain queryable historically.

## Review procedure

For every mutable business fact or relationship ask:

1. Can this value or relationship change?
2. What is the business-effective time of the historical event?
3. Can the event be entered or corrected later than that time?
4. If the source changes tomorrow, what should yesterday's event display or calculate with?
5. Does history need the old identity, old label, old scalar value, or the old full definition?
6. Must the system reconstruct the exact state at an arbitrary date?
7. Is the historical requirement legal/audit-critical, reporting-critical, or merely convenient?

Then classify the fact as current identity reference, historical relationship reference, value snapshot, or effective-dated history.

If the correct strategy depends on unresolved business policy, mark it as **DECISION REQUIRED** instead of guessing.

## Common failure modes

### Recording-time state pretending to be event-time truth

A historical event is entered late and the application copies the employee's or product's current state instead of the state that applied when the event occurred.

### Mutable reference pretending to be a value snapshot

A historical transaction stores only a foreign key to a mutable reference row. Updating the reference silently changes labels or attributes shown for the old transaction.

The foreign key may still correctly preserve identity; the mistake is claiming that all historical values were frozen.

### Snapshot without a source-of-truth definition

The same field appears in several tables, but nobody knows which copy is authoritative or when the snapshot is taken.

### Version table without version semantics

A table named `*_version` exists, but old versions are updated in place, making it a naming convention rather than true history.

### Audit timestamps mistaken for history

`updated_at` tells when the current row changed. It does not preserve previous values or business-effective periods.

### Soft delete mistaken for temporal modeling

Keeping a deleted row does not explain which definition or relationship was effective when a historical event occurred.

## Output expectation

Create a concise temporal decision table for consequential mutable facts:

| Fact / relationship | Can change? | Business-time expectation | Recording-time risk | Strategy | Reason |
|---|---|---|---|---|---|
| Product version used | Yes | Production must preserve the version used on the production date | Late entry may default to a newer current version | Version identity / effective-dated history | Version changes business meaning |
| Employee department | Yes | Production should belong to the department responsible on the production date | Late entry after transfer can capture the wrong department | Historical relationship reference or assignment history | Reporting depends on business-time ownership |
| Standard workhour | Yes | Past production may need the value actually applied then | Current lookup can rewrite old efficiency | Value snapshot or effective-dated history | Depends on business policy |

If a stored foreign key preserves only relationship identity while labels remain current, say so explicitly.
