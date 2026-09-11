# business-domain-design v0.2.2 focused regression

This compact regression covers a domain-general reconciliation failure observed during the first Level 3 benchmark.

## Eval — Human-resolved decision must reconcile the full artifact

### Setup

A business artifact previously contained:

- `D1 — DECISION REQUIRED` in the decision ledger;
- lifecycle/scenario/downstream sections that also described D1 as unresolved.

The human reviewer then resolves D1.

### FAIL

The artifact updates only the header or decision ledger:

```text
D1 — RESOLVED
```

but an active scenario still says:

```text
D1 must be decided before downstream design can continue.
```

or another active section still says `unresolved`, `undecided`, `remains unspecified`, or equivalent for D1.

This is a blocking semantic contradiction even if the resolved answer is correct elsewhere.

### PASS

The agent must:

1. promote the human resolution to **FACT**;
2. reconcile every affected active section whose meaning changed;
3. retain the old question only when explicitly labeled **RESOLVED / historical provenance**;
4. scan for stale unresolved wording tied to the resolved decision before declaring readiness.

### Non-goal

Do not rewrite unrelated sections merely for wording consistency. This regression tests semantic reconciliation after a human decision, not whole-document regeneration.