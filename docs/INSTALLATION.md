# Installation

Design Before Code can be used in two ways:

1. install one or more core Skills and invoke them directly;
2. optionally add the OpenSpec schema to orchestrate the full artifact workflow.

OpenSpec is optional. The Skills remain independently usable.

## 1. Get the repository

```bash
git clone https://github.com/Hello-DaTang/design-before-code.git
cd design-before-code
```

If you already have a clone:

```bash
git pull origin main
```

The canonical Skill sources live under:

```text
skills/
├── business-domain-design/
├── ux-flow-design/
├── data-model-design/
└── design-readiness-review/
```

Treat those directories as the source of truth. Agent-local copies are installation artifacts and do not update automatically when this repository changes.

## 2. Install the Skills into a project

Your AI agent must be able to discover project-local Skills. The exact discovery path is agent-specific.

The project-local layout validated during this project's Codex testing is:

```text
<target-project>/
└── .agents/
    └── skills/
        ├── business-domain-design/
        ├── ux-flow-design/
        ├── data-model-design/
        └── design-readiness-review/
```

Copy the **whole Skill directory**, not only `SKILL.md`, so any referenced support files remain available.

### Bash / WSL example

From the target project root:

```bash
mkdir -p .agents/skills

cp -R /path/to/design-before-code/skills/business-domain-design .agents/skills/
cp -R /path/to/design-before-code/skills/ux-flow-design .agents/skills/
cp -R /path/to/design-before-code/skills/data-model-design .agents/skills/
cp -R /path/to/design-before-code/skills/design-readiness-review .agents/skills/
```

### PowerShell example

From the target project root:

```powershell
New-Item -ItemType Directory -Force .agents\skills | Out-Null

Copy-Item -Recurse -Force C:\path\to\design-before-code\skills\business-domain-design .agents\skills\
Copy-Item -Recurse -Force C:\path\to\design-before-code\skills\ux-flow-design .agents\skills\
Copy-Item -Recurse -Force C:\path\to\design-before-code\skills\data-model-design .agents\skills\
Copy-Item -Recurse -Force C:\path\to\design-before-code\skills\design-readiness-review .agents\skills\
```

If your agent uses a different Skill directory, map the canonical `skills/<name>/` directories to that supported location instead. Do not rename the Skill itself unless the agent requires it.

## 3. Use only the Skills you need

You do not have to install all four Skills for every task.

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

For a full greenfield design pass, install all four and use:

```text
business-domain-design
→ ux-flow-design
→ data-model-design
→ design-readiness-review
```

A downstream Skill must not silently replace a missing upstream Skill or invent unresolved semantics. If required companion Skills are unavailable, stop and fix the installation instead of falling back invisibly.

## 4. Optional: install the OpenSpec schema

Prerequisite: the target project is already initialized for OpenSpec.

Copy:

```text
integrations/openspec/design-before-code/
```

from this repository to:

```text
<target-project>/openspec/schemas/design-before-code/
```

### Bash / WSL

```bash
mkdir -p openspec/schemas
cp -R /path/to/design-before-code/integrations/openspec/design-before-code \
  openspec/schemas/
```

### PowerShell

```powershell
New-Item -ItemType Directory -Force openspec\schemas | Out-Null
Copy-Item -Recurse -Force `
  C:\path\to\design-before-code\integrations\openspec\design-before-code `
  openspec\schemas\
```

Validate the copied schema:

```bash
openspec schema validate design-before-code
openspec schema which design-before-code
```

Create a change with the schema:

```bash
openspec new change my-feature --schema design-before-code
```

Or make it the project default in `openspec/config.yaml`:

```yaml
schema: design-before-code
```

The schema expects the four companion Skills to be discoverable by the agent.

## 5. Human approval rule

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

## 6. Updating installed Skills

If you copied the Skills into a project, a later `git pull` in the Design Before Code repository does **not** update those project-local copies.

After updating this repository, recopy the Skill directories:

```bash
git pull origin main

cp -R /path/to/design-before-code/skills/business-domain-design /target/project/.agents/skills/
cp -R /path/to/design-before-code/skills/ux-flow-design /target/project/.agents/skills/
cp -R /path/to/design-before-code/skills/data-model-design /target/project/.agents/skills/
cp -R /path/to/design-before-code/skills/design-readiness-review /target/project/.agents/skills/
```

Use `-Force` / replacement semantics appropriate to your platform.

For a personal development environment, symlinks can reduce this synchronization work, but copied directories are simpler and more portable for shared projects.

## 7. Verify the installed versions

From the Design Before Code repository:

```bash
grep -R "Current behavior target" skills/*/SKILL.md
```

At the time of this document:

```text
business-domain-design   v0.2.2
ux-flow-design           v0.2.1
data-model-design        v0.2.3
design-readiness-review  v0.2
```

If a project-local copy reports older versions, refresh that copy from the canonical `skills/` directory.

## 8. Recommended first use

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
