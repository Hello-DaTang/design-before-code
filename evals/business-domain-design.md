# business-domain-design Evaluation Cases

These compact evals follow `docs/EVALUATION-PROTOCOL.md`.

The goal is not to force one domain model. The goal is to verify that important business semantics become explicit before UX, data, or implementation design.

## Eval 1 — Do not jump to implementation artifacts

### Prompt

> We need an employee leave-request feature. Employees submit leave, managers approve or reject it, and HR can view history. Please design the feature.

### Pass criteria

The agent first identifies actors, goals, concepts, lifecycle/events, rules, and unresolved decisions. It does not begin with tables, APIs, controllers, pages, or classes.

---

## Eval 2 — Detect different lifecycles hidden on one screen

### Prompt

> Our shift screen has plan quantity and actual quantity on the same form. Supervisors set plan quantity before the shift. Operators fill actual quantity after the shift. Are these simply two fields of one business record?

### Pass criteria

The agent explicitly analyzes lifecycle, timing, ownership, and correction behavior. It recognizes that Plan and Actual may be separate concepts even if shown together, while allowing a single concept if the business confirms they always share one lifecycle.

---

## Eval 3 — Modeling-depth gate rejects ritual DDD

### Prompt

> We need a simple category maintenance feature: code, name, enabled/disabled, sort order. Please model the business domain.

### Pass criteria

The agent explicitly classifies the scope as Plain CRUD (or equivalent low-complexity modeling) and keeps the artifact simple. It does not invent bounded contexts, aggregates, domain events, repositories, or state machines without a real need.

---

## Eval 4 — Event vs state

### Prompt

> An order has states Pending Payment, Paid, Shipped, Completed. Payment can fail and the customer may retry. Is payment just part of order status?

### Pass criteria

The agent distinguishes payment events/attempt lifecycle from order state and explains why retries may imply a separate business concept or at least a separate lifecycle. It does not equate `PaymentSucceeded` with a generic order status field.

---

## Eval 5 — Recommendation is not requirement

### Prompt

> Stakeholders say products can be disabled and historical orders must remain queryable. The designer proposes that product codes can never change and disabled products can never be re-enabled. Are those confirmed business rules?

### Pass criteria

The agent separates explicit facts from inferences/recommendations and marks consequential unsupported rules for human confirmation.

---

## Eval 6 — Exception reveals hidden concept

### Prompt

> An order normally ships once, but the business says it may later support shipping some items first and the rest later. We have not decided whether partial shipment is actually needed. Model the current business.

### Pass criteria

The agent exposes Shipment as a potentially independent lifecycle without prematurely forcing multi-shipment infrastructure. It marks the unresolved future policy as DECISION REQUIRED when it materially changes downstream design.

---

## Eval 7 — Same word, different meanings

### Prompt

> In our meetings, "version" sometimes means the product's engineering revision and sometimes means a saved draft revision of a configuration screen. The requirement says production records must store the version used.

### Pass criteria

The agent identifies the terminology collision and refuses to silently treat both uses of "version" as one concept. It asks/marks which version the production record must preserve.

---

## Eval 8 — Downstream implication without designing downstream artifacts

### Prompt

> A refund can be requested for only one item in an order, can be rejected, and an approved refund may later fail at the payment provider. Model the business before implementation.

### Pass criteria

The agent identifies distinct concepts/lifecycles such as refund request/decision and refund execution when justified, and states implications for later UX/data design without prematurely defining tables or API endpoints.

---

## Eval 9 — Event-first discovery reveals behavior without forcing event sourcing

### Prompt

> A purchase request is submitted by an employee, may be returned for correction, approved by a manager, then converted into a purchase order by procurement. We are not sure whether “request” and “order” are really the same thing.

### Pass criteria

The agent uses an event-first sequence such as Submitted → Returned/Approved → PurchaseOrderCreated to expose lifecycle and responsibility differences. It may identify separate concepts, but it must not conclude that the implementation must use event sourcing or a message bus.

---

## Eval 10 — Deep modeling only when boundaries earn their place

### Prompt

> Our platform has Sales, Billing, Fulfillment, and Support teams. The word “customer” means the account owner in Billing but the delivery recipient in Fulfillment. Policies and ownership differ between these areas. Should we use one business model for everything?

### Pass criteria

The agent recognizes that deeper domain/boundary analysis is justified, surfaces terminology/ownership conflicts, and may propose candidate capabilities/bounded contexts. It must not equate each boundary automatically with a microservice or deployment unit.
