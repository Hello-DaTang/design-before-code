# ux-flow-design Evaluation Cases

These evals test whether the skill produces reviewable task flows rather than merely prettier page structures.

Use `docs/EVALUATION-PROTOCOL.md`: routine edits should run focused unit evals plus a small regression sample, not a full A/B benchmark.

## Eval 1 — Do not mirror backend entities into menus

### Prompt

> The backend has Product, ProductVersion, ProductStandard, and ProductionRecord entities. Design the frontend menu and pages.

### Pass criteria

The agent must first identify user tasks and challenge whether every entity deserves a top-level menu/page. It should consider contextual version/standard management under Product when appropriate.

### Fail examples

- Creates four top-level menu items solely because four entities exist.
- Says “one CRUD page per table” without task analysis.

---

## Eval 2 — High-frequency task should not inherit admin workflow

### Prompt

> Production operators enter one shift record many times per day. Administrators also maintain products and versions. Can all functions live in the same management page?

### Pass criteria

The agent recognizes different actors/tasks/frequencies and evaluates separate operational vs administrative entry paths rather than forcing one giant CRUD screen.

---

## Eval 3 — Page vs dialog decision

### Prompt

> Editing a product version includes basic info, standard parameters, effective-date behavior, history, and downstream impact. Should it be a modal dialog because editing usually uses dialogs in our admin framework?

### Pass criteria

The agent challenges framework convention and prefers a dedicated page or other persistent surface if the task needs substantial context/history. It explains the decision in task terms.

---

## Eval 4 — Empty/error/recovery states

### Prompt

> Design a production-record list page with search, add, edit, and delete.

### Pass criteria

Besides the happy path, the agent discusses relevant empty/loading/failure/permission states and gives a useful recovery path. It does not stop at a CRUD toolbar description.

---

## Eval 5 — Backdated entry must not default blindly to current state

### Prompt

> September 1 production is entered on September 10. The employee transferred departments and the current product version also changed between those dates. Design the entry flow.

### Pass criteria

The agent makes the business-effective date/context visible and warns against silently defaulting department/version from September 10 current state. It exposes unresolved business rules if historical resolution is unclear.

---

## Eval 6 — Consequential action needs context, not generic confirmation

### Prompt

> An administrator can switch the current product version. The switch may affect which standard parameters operators use for future entries. Design the interaction.

### Pass criteria

The agent places the action near version/effective-date context, explains consequences, and avoids a generic “Are you sure?” as the entire safety design. If effective-date vs immediate-switch semantics are unresolved, it marks DECISION REQUIRED.

---

## Eval 7 — Concurrent edit recovery

### Prompt

> Two supervisors may open the same production record. One saves first. The other then clicks Save with older data. Design the UX.

### Pass criteria

The agent defines how conflict is communicated and how the second user's work can be preserved/reloaded/compared if recoverability is desired. A generic failure toast alone fails.

---

## Eval 8 — Do not show every stored field

### Prompt

> The database record has id, tenant_id, creator, updater, create_time, update_time, deleted, product_version_id, dept_id, owner_id, quantities, work time, and remark. Which fields should the edit form show?

### Pass criteria

The agent chooses information based on the user's task and business decisions rather than exposing database columns mechanically. Technical/audit fields are hidden unless useful in a separate review/history context.

---

## Eval 9 — Primary task and next action

### Prompt

> A manager opens a production exception detail. They need to understand what went wrong and then either correct it, send it back, or mark it accepted. Design the screen.

### Pass criteria

The agent organizes evidence/context before actions and clarifies which action is primary or under what conditions actions differ. It does not present three equal buttons without decision context.

---

## Eval 10 — UX promise creates downstream requirement

### Prompt

> Users want to partially fill a complex form, leave, and resume tomorrow without losing work. We are only designing UX now.

### Pass criteria

The agent may design save/resume behavior, but must explicitly surface the downstream implication that persistent draft state/API/data support is required. It must not silently promise a UX the backend cannot represent.

---

## Eval 11 — Avoid speculative screens

### Prompt

> We may support dashboards, workflow approval, batch import, mobile entry, and AI suggestions someday. None are currently required. Design the first release navigation.

### Pass criteria

The agent excludes speculative top-level screens unless there is a current user job. It records future possibilities without letting them drive current IA complexity.

---

## Eval 12 — Business ambiguity blocks UX choice

### Prompt

> A production record can be “deleted,” but nobody has decided whether that means accidental-record removal, business voiding, or correction with audit history. Design the delete button flow.

### Pass criteria

The agent refuses to finalize a destructive interaction until the business meaning is clarified. It compares consequences and marks DECISION REQUIRED rather than inventing a generic delete confirmation.

---

## Eval 13 — Named protagonist makes the journey reviewable

### Prompt

> Design the workflow for handling a quality exception. Operators report it, supervisors decide what to do, and managers sometimes review severe cases.

### Pass criteria

The agent does not output only an abstract page graph. It defines at least one key flow around an explicit role/protagonist, goal, entry point, numbered steps, decision point, success outcome, and a relevant failure/recovery branch.

---

## Eval 14 — Surface closure catches missing states

### Prompt

> Our IA has Request List, Request Detail, Approval Drawer, and History. The happy path is already documented. Is the UX ready?

### Pass criteria

The agent walks the surface inventory and checks applicable default/loading/empty/error/permission/conflict/recovery states. It must identify missing critical state behavior instead of declaring the UX ready because the happy-path journey exists.

---

## Eval 15 — Behavioral UX stays separate from visual design

### Prompt

> We need to validate whether the refund workflow makes sense. Please choose our color palette, typography, spacing scale, and component library too.

### Pass criteria

The agent keeps the current task focused on behavioral flow, states, information hierarchy, and recovery. It may note that visual-system work belongs to a separate design workflow; it should not let visual polish substitute for unresolved refund behavior.
