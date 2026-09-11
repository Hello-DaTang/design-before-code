# Alpha Release Checklist

This checklist is for the first **repository-wide** Design Before Code release.

## Proposed release

```text
v0.1.0-alpha.1
```

This repository-level version is independent of the versions inside individual Skills.

Current Skill versions:

- `business-domain-design` — v0.2.2
- `ux-flow-design` — v0.2.1
- `data-model-design` — v0.2.3
- `design-readiness-review` — v0.2

No repository-wide tag has been created yet.

## Evidence required before tagging

- [x] Project Charter exists and defines mission / anti-drift rules.
- [x] Method synthesis and attribution are documented.
- [x] Evaluation Protocol exists and discourages repeated expensive benchmarks.
- [x] All four core Skills have minimum-usable versions.
- [x] Phase B cross-artifact smoke passed.
- [x] Phase C OpenSpec runtime + negative/positive approval-gate validation passed.
- [x] Phase D realistic Level 3 benchmark reached `READY_FOR_HUMAN_APPROVAL`.
- [x] Phase D findings were generalized into compact Skill fixes and focused regression definitions.
- [x] Installation documentation exists.
- [x] Agent compatibility claims distinguish verified from unverified support.
- [x] A compact full-chain quick-start example exists.
- [x] README reflects the current versions and milestone status.
- [x] CHANGELOG separates Skill versions from repository release versions.

## Static release checks

Before creating the tag:

- [ ] Pull the latest `main` into the local release clone and confirm the working tree is clean.
- [x] Verify the four `SKILL.md` frontmatters/names match their directories.
- [x] Verify all documented Skill version strings match the files themselves.
- [ ] Verify README / docs relative links render correctly on GitHub.
- [x] Validate the bundled OpenSpec schema **from an initialized temporary/target OpenSpec project after copying the bundle to `openspec/schemas/design-before-code/`**. The repository root is the bundle source, not itself an installed OpenSpec schema location.
- [x] Confirm no Phase D benchmark-specific domain rule leaked into a core Skill.
- [x] Confirm no temporary Phase D OpenSpec artifacts were added to the repository as product templates.
- [x] Confirm `human-approval.md` remains a human-only contract in integration instructions.
- [x] Confirm examples are labeled illustrative and evals are labeled evidence, not canonical product designs.
- [x] Review `CHANGELOG.md` Unreleased section and freeze release notes as `v0.1.0-alpha.1 — 2026-09-11`.

### Observed OpenSpec release-check — PASS (2026-09-11)

Validation was run from a clean temporary OpenSpec project after installing the repository bundle into the documented project-local schema location.

Observed discovery:

```text
Project schemas:
  design-before-code

Package schemas:
  spec-driven
```

Observed validation:

```text
Validating design-before-code...
  Checking schema.yaml exists...
  Parsing YAML...
  Validating schema structure...
  Checking template files...
  Dependency graph validation passed (via parseSchema)
✓ Schema 'design-before-code' is valid
```

Running the same validation command directly from the Design Before Code repository root still reports only `spec-driven`. That is expected: `integrations/openspec/design-before-code/` is the distributable bundle source, while OpenSpec discovers the project schema only after installation to `openspec/schemas/design-before-code/` (or another supported discovery location).

Recommended PowerShell validation from the repository root:

```powershell
$test = Join-Path $env:TEMP "dbc-openspec-release-check"
Remove-Item -Recurse -Force $test -ErrorAction SilentlyContinue
New-Item -ItemType Directory -Force $test | Out-Null

openspec init $test --tools none

New-Item -ItemType Directory -Force "$test\openspec\schemas" | Out-Null
Copy-Item -Recurse -Force `
  ".\integrations\openspec\design-before-code" `
  "$test\openspec\schemas\"

Push-Location $test
openspec schema which --all
openspec schema validate design-before-code --verbose
Pop-Location
```

These are structural/release checks. They do **not** require another paid Level 3 model benchmark.

## Release boundary

The alpha may claim:

- experimental greenfield design workflow;
- four independently usable core Skills;
- tested OpenSpec orchestration at the documented experimental level;
- one realistic Level 3 readiness benchmark;
- artifact-based mixed-agent continuation evidence;
- explicit human approval boundary.

The alpha must **not** claim:

- production stability;
- support for every coding agent;
- actor-authenticated approval enforcement from OpenSpec alone;
- legacy/brownfield reverse-engineering coverage;
- complete visual-design-system generation;
- proof that every domain or database will be modeled correctly without human review.

## Tagging recommendation

After the static checks pass:

```bash
git tag -a v0.1.0-alpha.1 -m "Design Before Code v0.1.0-alpha.1"
git push origin v0.1.0-alpha.1
```

Then create a GitHub Release from that tag using the finalized `CHANGELOG.md` release section as the basis for release notes.

Do not delete or rewrite the Phase B/C/D validation evidence after release; future improvements should append new evidence or focused regressions rather than rewriting history.
