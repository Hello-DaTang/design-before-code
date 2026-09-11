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

- [ ] Pull the latest `main` into a clean local clone.
- [ ] Verify the four `SKILL.md` frontmatters parse and their names match their directories.
- [ ] Verify all documented Skill version strings match the files themselves.
- [ ] Verify README / docs relative links render correctly on GitHub.
- [ ] Run `openspec schema validate design-before-code` with the documented supported OpenSpec version or current compatible CLI.
- [ ] Confirm no benchmark-specific domain rule leaked into a core Skill.
- [ ] Confirm no temporary Phase D OpenSpec artifacts were added to the repository as product templates.
- [ ] Confirm `human-approval.md` remains a human-only contract in integration instructions.
- [ ] Confirm examples are labeled illustrative and evals are labeled evidence, not canonical product designs.
- [ ] Review `CHANGELOG.md` Unreleased section and freeze release notes.

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

Then create a GitHub Release from that tag using the finalized `CHANGELOG.md` Unreleased section as the basis for release notes.

Do not delete or rewrite the Phase B/C/D validation evidence after release; future improvements should append new evidence or focused regressions rather than rewriting history.
