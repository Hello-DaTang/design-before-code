# data-model-design v0.2.3 focused regression

This compact regression covers a domain-general dependency/integrity miss observed during the first Level 3 benchmark.

## Eval A — Dual-path identity must agree or be removed

### Setup

Entity `A` stores both:

```text
A.b_id
A.c_id → C.b_id
```

so the same business identity `B` is reachable directly and transitively.

### FAIL

Keep both stored paths while merely stating that they "should match" or are useful for query convenience, with no enforceable agreement mechanism.

This permits contradictory data such as direct path → B1 while transitive path → B2.

### PASS

Choose one of:

1. remove the redundant path and keep one authoritative relationship; or
2. retain both only with a concrete consistency mechanism that forces agreement, such as an appropriate composite UNIQUE/FK design, trigger, generated constraint strategy, or equivalent target-database invariant.

The artifact must explicitly classify the duplicate identity path in the dependency/redundancy walk.

---

## Eval B — Reference + snapshot semantics must be explicit

### Setup

A historical outcome stores both:

- a reference to a mutable rule/definition; and
- copied values used when the outcome was produced.

### PASS criteria

The model must state:

- what the reference is authoritative for;
- what the snapshots are authoritative for;
- whether the referenced row may mutate after use;
- how historical interpretation remains stable;
- what mechanism prevents rewriting applied history when immutability is required.

Snapshots alone are not sufficient justification for silently rewriting an already-applied historical definition.