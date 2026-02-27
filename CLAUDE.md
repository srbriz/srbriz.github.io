# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a GitHub Pages project repository bootstrapped with **Speckit** — a spec-driven development workflow that takes features from natural language description through specification, planning, task generation, and implementation.

When architecture, tooling, or workflow changes are introduced, update this document and related project docs in the same change to keep documentation current.

## Speckit Workflow

Features are developed using a structured pipeline of slash commands. Each command builds on the artifacts from the prior step.

### Command Pipeline (in order)

| Command                          | Purpose                                                           |
| -------------------------------- | ----------------------------------------------------------------- |
| `/speckit.constitution`          | Create/update project principles that govern all design decisions |
| `/speckit.specify <description>` | Create a feature spec from a natural language description         |
| `/speckit.clarify`               | Interactively resolve spec ambiguities (run before planning)      |
| `/speckit.plan`                  | Generate technical implementation plan and design artifacts       |
| `/speckit.tasks`                 | Break the plan into an ordered, dependency-aware `tasks.md`       |
| `/speckit.analyze`               | Read-only consistency check across spec/plan/tasks                |
| `/speckit.checklist <focus>`     | Generate requirements-quality checklists (e.g., UX, security)     |
| `/speckit.implement`             | Execute tasks from `tasks.md` phase by phase                      |
| `/speckit.taskstoissues`         | Convert tasks to GitHub Issues (requires GitHub remote)           |

### Feature Branch Convention

Each feature lives on a branch named `NNN-short-name` (e.g., `001-user-auth`). The numeric prefix is auto-assigned by `create-new-feature.sh` by finding the highest existing number across branches and spec directories and incrementing by 1.

`/speckit.specify` creates the branch and bootstraps the spec directory automatically. Never manually create feature branches or spec directories.

### Feature Directory Structure

All feature artifacts are stored under `specs/NNN-short-name/`:

```
specs/001-feature-name/
  spec.md          # Business requirements (created by /speckit.specify)
  plan.md          # Technical plan (created by /speckit.plan)
  tasks.md         # Executable task list (created by /speckit.tasks)
  research.md      # Optional: tech decisions and rationale
  data-model.md    # Optional: entities and relationships
  quickstart.md    # Optional: integration test scenarios
  contracts/       # Optional: API/interface contracts
  checklists/      # Optional: requirements quality checklists
    requirements.md
    ux.md, security.md, etc.
```

Scripts use the current git branch (or `SPECIFY_FEATURE` env var) to resolve the active feature directory.

## Key Files

- `.specify/memory/constitution.md` — Project principles; governs all features. Update via `/speckit.constitution`.
- `.specify/templates/` — Templates copied when creating new artifacts (spec, plan, tasks, checklists, agent context).
- `.specify/scripts/bash/common.sh` — Shared functions used by all scripts (path resolution, branch detection).
- `.specify/scripts/bash/create-new-feature.sh` — Creates feature branch + spec directory. Called by `/speckit.specify`.
- `.specify/scripts/bash/setup-plan.sh` — Copies plan template into active feature dir. Called by `/speckit.plan`.
- `.specify/scripts/bash/check-prerequisites.sh` — Validates feature branch and required artifacts. Called by most commands.
- `.specify/scripts/bash/update-agent-context.sh` — Updates agent-specific context files (CLAUDE.md, GEMINI.md, etc.) from `plan.md` data.

## Script Usage

All scripts must be run from the repository root. They auto-detect the repo root via git or `.specify/` marker:

```bash
# Create a new feature (called automatically by /speckit.specify)
.specify/scripts/bash/create-new-feature.sh --json --number 5 --short-name "user-auth" "Add user authentication"

# Check prerequisites for planning phase
.specify/scripts/bash/check-prerequisites.sh --json

# Check prerequisites for implementation phase (requires tasks.md)
.specify/scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks

# Update CLAUDE.md with tech info from active plan.md
.specify/scripts/bash/update-agent-context.sh claude
```

## Constitution

The constitution (`.specify/memory/constitution.md`) defines non-negotiable project principles. It must be initialized before any features are created. `/speckit.analyze` treats constitution violations as CRITICAL issues. Any principle change requires a separate `/speckit.constitution` run — never modify it directly to work around a violation.

## Checklist Philosophy

Checklists generated by `/speckit.checklist` are **requirements quality tests**, not implementation tests. Each item asks whether a requirement is complete, clear, consistent, and measurable — not whether the code works. Incomplete checklists block `/speckit.implement` (user must confirm to override).

## GitHub MCP

This project uses the [GitHub MCP server](https://github.com/github/github-mcp-server) (configured in `.mcp.json`) to give Claude Code access to GitHub APIs — used by `/speckit.taskstoissues` and any GitHub-integrated commands.

Authentication is read from the `GITHUB_PERSONAL_ACCESS_TOKEN` environment variable. `task claude` sets this automatically via `gh auth token`. If running `claude` directly outside of `task`, set it manually:

```bash
export GITHUB_PERSONAL_ACCESS_TOKEN=$(gh auth token)
```

## Active Technologies

- **Output**: Static webpage hosted on GitHub Pages
- **Formatting**: Prettier (v3.8.1) with 4-space tab width
- **Linting**: pre-commit hooks (see `.pre-commit-config.yaml`)

## Linting

Linting runs via **pre-commit**. Never skip hooks with `--no-verify`.

**Active hooks:**

- `trailing-whitespace` — removes trailing whitespace
- `end-of-file-fixer` — ensures files end with a newline
- `check-yaml` — validates YAML syntax
- `check-ast` — validates Python AST
- `check-merge-conflict` — detects unresolved merge conflicts
- `mixed-line-ending` — enforces consistent line endings
- `prettier` — formats all text files (4-space tabs); excludes `.specify/` and `.claude/`

To run linting manually:

```bash
pre-commit run --all-files
```

## Task Runner

This repository uses [Task](https://taskfile.dev) (`go-task`) as its task runner. Install it with:

```bash
brew install go-task
```

Available tasks:

| Task           | Description                           |
| -------------- | ------------------------------------- |
| `task install` | Install all project dependencies      |
| `task claude`  | Launch Claude Code (interactive)      |
| `task lint`    | Run pre-commit hooks across all files |
| `task build`   | No-op (placeholder)                   |
| `task clean`   | No-op (placeholder)                   |

Run `task` with no arguments to list all available tasks.

## Recent Changes
