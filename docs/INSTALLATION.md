# Installation

Design Before Code supports a **zero-clone installation path** for normal users.

You only need to clone this repository if you want to contribute to Design Before Code itself. For ordinary use, install the Skills and optional OpenSpec schema directly from GitHub into your existing project.

## 1. Prerequisites

For the recommended zero-clone path:

- Node.js 20+ with `npx` available;
- an AI coding agent supported by the Skills CLI;
- OpenSpec only if you want the orchestrated workflow.

Check Node / npx:

```bash
node --version
npx --version
```

## 2. Install the Skills directly from GitHub

From your existing application/project root:

```bash
npx -y skills add Hello-DaTang/design-before-code --all -a codex -y
```

The first `-y` belongs to `npx` and suppresses npm's package-install confirmation. The final `-y` belongs to the Skills CLI and accepts its install prompt.

This scans the repository's canonical `skills/` directory and installs all four core Skills into the project-local location used by the selected/universal agent installation strategy.

The Skills CLI may internally fetch or clone the GitHub repository while resolving the source. Zero-clone here means the user does **not** need to create or maintain a Design Before Code working clone inside the application project.

Replace `codex` with another agent supported by the Skills CLI, for example:

```bash
npx -y skills add Hello-DaTang/design-before-code --all -a github-copilot -y
npx -y skills add Hello-DaTang/design-before-code --all -a cursor -y
npx -y skills add Hello-DaTang/design-before-code --all -a claude-code -y
```

The four core Skills are:

```text
business-domain-design
ux-flow-design
data-model-design
design-readiness-review
```

The Codex project-local layout validated by this project is:

```text
<target-project>/
└── .agents/
    └── skills/
        ├── business-domain-design/
        ├── ux-flow-design/
        ├── data-model-design/
        └── design-readiness-review/
```

Other agents may use different locations or symlink strategies; let the Skills CLI perform that mapping instead of manually copying directories when possible.

## 3. Use the Skills without OpenSpec

OpenSpec is optional. You can use any Skill independently.

Typical standalone use:

```text
rough business requirement
→ business-domain-design
```

```text
stable business model
→ ux-flow-design
```

```text
stable business + UX semantics
→ data-model-design
```

```text
business + UX + data artifacts
→ design-readiness-review
```

For a full greenfield design pass:

```text
business-domain-design
→ ux-flow-design
→ data-model-design
→ design-readiness-review
```

A downstream Skill must not silently replace a missing upstream Skill or invent unresolved semantics. If a required Skill is unavailable, fix the installation instead of falling back invisibly.

## 4. Enhance an existing OpenSpec project — no clone required

If the target project is not initialized yet:

```bash
openspec init
```

Install the four Design Before Code Skills:

```bash
npx -y skills add Hello-DaTang/design-before-code --all -a codex -y
```

Then fetch only the OpenSpec schema subdirectory directly into the current project:

```bash
npx -y degit Hello-DaTang/design-before-code/integrations/openspec/design-before-code openspec/schemas/design-before-code
```

This installs the schema files without creating a user-managed Design Before Code clone or adding its Git history to your application repository.

Validate discovery and structure:

```bash
openspec schema which --all
openspec schema validate design-before-code --verbose
```

Expected discovery includes:

```text
Project schemas:
  design-before-code

Package schemas:
  spec-driven
```

Then either use the schema per change:

```bash
openspec new change my-feature --schema design-before-code
```

or make it the project default in `openspec/config.yaml`:

```yaml
schema: design-before-code
```

At that point the normal OpenSpec project is enhanced with the Design Before Code flow:

```text
proposal
→ business-model
→ ux-flow
→ data-model
→ design-readiness
→ human-approval
→ specs
→ technical-design
→ tasks
→ apply
```

The schema expects the four companion Skills to be discoverable by the agent.

## 5. Recommended first-time sequence

For a new/current application repository, the intended user experience is:

```bash
openspec init
npx -y skills add Hello-DaTang/design-before-code --all -a codex -y
npx -y degit Hello-DaTang/design-before-code/integrations/openspec/design-before-code openspec/schemas/design-before-code
openspec schema validate design-before-code --verbose
```

Then start a change:

```bash
openspec new change my-feature --schema design-before-code
```

No user-managed Design Before Code clone is required.

This zero-clone path was smoke-tested from a clean temporary project on 2026-09-11: all four Skills installed under `.agents/skills/`, `design-before-code` was discovered as a project schema, and verbose schema validation passed YAML, structure, template, and dependency-graph checks.

## 6. Human approval rule

The OpenSpec flow intentionally stops at:

```text
READINESS: READY_FOR_HUMAN_APPROVAL
```

The AI agent must not create or edit `human-approval.md` on the reviewer's behalf.

After reviewing the Business, UX, Data, and readiness artifacts, the human reviewer may create the approval artifact and record:

```text
APPROVAL: APPROVED
```

Only then should the workflow continue to specs, technical design, and tasks.

OpenSpec artifact dependencies do not authenticate who wrote the approval file. If an organization needs identity-level enforcement, add external CI, hook, or review controls.

## 7. Updating an installed project

### Update Skills

Use the Skills CLI update flow when available:

```bash
npx -y skills update
```

Or reinstall this collection from the repository using the same `npx -y skills add ...` command.

### Update the OpenSpec schema

The schema directory is ordinary project-local content. For an explicit replacement, remove only the installed Design Before Code schema directory, then fetch it again:

#### PowerShell

```powershell
Remove-Item -Recurse -Force .\openspec\schemas\design-before-code
npx -y degit Hello-DaTang/design-before-code/integrations/openspec/design-before-code openspec/schemas/design-before-code
openspec schema validate design-before-code --verbose
```

#### Bash / WSL

```bash
rm -rf openspec/schemas/design-before-code
npx -y degit Hello-DaTang/design-before-code/integrations/openspec/design-before-code openspec/schemas/design-before-code
openspec schema validate design-before-code --verbose
```

Do not delete other project-local schemas.

## 8. Version pinning for releases

During active alpha development, the commands above install from the repository's current default branch.

For reproducible release usage, prefer a release/tag-specific GitHub source once the repository-wide tag exists. Both the Skills CLI and `degit` support GitHub refs/tags in their source forms.

Release-specific commands will be documented with each repository release so users can choose between:

- latest/default branch for active testing;
- a pinned alpha/stable tag for reproducible installation.

## 9. Manual clone/copy fallback

Cloning remains useful for project contributors, offline/local modification, or environments where `npx` cannot be used:

```bash
git clone https://github.com/Hello-DaTang/design-before-code.git
cd design-before-code
```

The canonical Skill sources are under:

```text
skills/
```

and the OpenSpec bundle is under:

```text
integrations/openspec/design-before-code/
```

If manually copying a Skill, copy the **whole Skill directory**, not only `SKILL.md`, so referenced support files remain available.

## 10. Current Skill versions

```text
business-domain-design   v0.2.2
ux-flow-design           v0.2.1
data-model-design        v0.2.3
design-readiness-review  v0.2
```

## 11. Recommended first use

Start with a real but bounded greenfield requirement. Do not start with a giant legacy system or organization-wide architecture exercise.

Give the agent the requirement and make the first instruction explicit, for example:

```text
Use business-domain-design on this requirement.
Do not write implementation code.
Make consequential unresolved business decisions visible for human review.
```

After the business model is accepted, continue to UX, Data, and readiness rather than asking one agent call to invent everything at once.

For the full OpenSpec lifecycle and approval semantics, see:

- [`../integrations/openspec/design-before-code/README.md`](../integrations/openspec/design-before-code/README.md)
- [`../integrations/openspec/design-before-code/INTEGRATION.md`](../integrations/openspec/design-before-code/INTEGRATION.md)
- [`AGENT-COMPATIBILITY.md`](AGENT-COMPATIBILITY.md)
