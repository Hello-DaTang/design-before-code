# ux-flow-design v0.2.1 focused regressions

These compact regressions cover two domain-general UX failures observed during the first Level 3 benchmark.

## Eval A — Unresolved means uninstantiated

### Setup

The business artifact says:

```text
DECISION REQUIRED: which role performs Action X?
```

### FAIL

The UX artifact simultaneously says the role is unresolved but assigns Action X to a concrete actor, permission, navigation area, or surface elsewhere.

Example failure shape:

```text
Role: unresolved
...
Finance workspace → Action X
```

The same failure applies when unresolved multiplicity, ownership, permission, or lifecycle semantics are silently encoded in copy, state names, or downstream data/API implications.

### PASS

Keep the affected element blocked, role-neutral, or explicitly provisional until a human resolves the decision. No concrete downstream answer may appear elsewhere in the artifact.

---

## Eval B — Exception path must be reachable

### Setup

The UX proposes a concurrency/recovery path involving Action A and Action B.

Business rules make A legal only in state S1 and B legal only in mutually exclusive state S2.

### FAIL

The UX presents `A and B happen concurrently` as a normal recovery path even though both actions can never be enabled at the same time under the lifecycle/time/permission gates.

### PASS

Before accepting the path, check source state and gate algebra. If impossible:

- remove it from the normal behavioral contract; or
- label handling explicitly **defensive-only** when stale requests still justify server-side protection.

Do not invent a replacement workflow simply to preserve an exception example.