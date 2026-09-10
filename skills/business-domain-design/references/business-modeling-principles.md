# Business Modeling Principles

Use these principles when `business-domain-design` needs deeper reasoning.

## 1. Start from outcomes, not software surfaces

A page called "Production Record" does not prove there is one business concept called ProductionRecord.

Ask what people are trying to accomplish and what exists before/after the interaction.

## 2. Lifecycle is a strong modeling signal

Two groups of fields that appear together may represent different concepts when they:

- begin at different times;
- are created by different actors;
- can exist independently;
- have different state transitions;
- are cancelled/corrected differently;
- become final at different times.

Examples:

- plan vs execution;
- order vs payment;
- order vs shipment;
- refund request vs refund transaction;
- request vs approval decision.

## 3. Responsibility matters

A useful business concept owns some responsibility or rule.

If a proposed concept exists only because fields are visually grouped or a controller needs a DTO, challenge it.

## 4. Identity is not the same as a label

Ask whether the business must distinguish two occurrences over time even when labels are identical.

Stable identity often matters for durable concepts such as an order, product version, shipment, approval request, or production event.

## 5. Events are not states

Separate what happened from the resulting condition.

Examples:

- PaymentSucceeded is an event; Paid is a resulting order/payment state.
- ShipmentDispatched is an event; Shipped is a state.
- VersionActivated is an event; Current is a state/role.

This distinction helps expose separate lifecycles without forcing event sourcing.

## 6. Avoid ritual DDD

Do not require aggregates, bounded contexts, domain services, repositories, commands, events, or value objects simply because the system has business logic.

Use those ideas only when they make the business model clearer or protect real invariants.

A small CRUD system may need only clear concepts, relationships, rules, and scenarios.

## 7. Separate source statements from design conclusions

Always distinguish:

- FACT;
- INFERENCE;
- RECOMMENDATION;
- ASSUMPTION;
- DECISION REQUIRED.

A common business pattern is evidence for a recommendation, not proof of a requirement.

## 8. Challenge generic nouns

Words such as `config`, `record`, `detail`, `type`, `status`, `data`, and `info` often hide missing business meaning.

Ask what the thing actually represents.

Prefer names such as:

- ProductionPlan;
- PaymentAttempt;
- Shipment;
- RefundRequest;
- ProductVersion;

when those are the real concepts.

## 9. Current complexity must be justified by current knowledge

Do not build flexible engines for hypothetical futures.

If future multi-payment, partial shipment, multi-level approval, or dynamic rules are explicitly uncertain, expose the decision and avoid pretending the future model is already known.

## 10. Exceptions reveal hidden concepts

The happy path often makes a weak model look correct.

Stress it with:

- partial completion;
- retry;
- cancellation;
- correction;
- reassignment;
- late entry;
- changing policy/version;
- conflicting actors;
- duplicate attempt.

If the exception cannot be described without inventing hidden state or overloaded meanings, revisit the concepts.

## 11. Downstream artifacts must inherit business semantics

This skill does not design pages or tables, but it should state constraints they must respect.

Example:

If a Payment can fail and retry independently of the Order lifecycle, downstream UX and data design must not collapse "order status" and "payment attempt" into one undifferentiated field.

## 12. Explain the model to a normal developer

A model that is technically sophisticated but cannot answer "what is this thing and why does it exist?" is not ready.

Use diagrams, concrete examples, lifecycle tables, and short decision summaries instead of jargon-heavy prose.
