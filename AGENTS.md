# Repository Guidelines

This file provides guidance to Claude Code (claude.ai/code), Codex CLI, and other AI agents when working with code in this repository.

## Project Overview

`agent-skills` is a collection of agent skills — packaged instructions that extend AI coding agents with specialized capabilities. Install via `npx skills add aliasunder/agent-skills`.

## Repository Structure

```
agent-skills/
  README.md
  CONTRIBUTING.md
  LICENSE
  package.json
  skills/
    <skill-name>/
      SKILL.md                          # Skill definition (frontmatter: name, description)
      references/                       # Detailed reference docs loaded on demand
      assets/                           # Static files (dashboards, templates)
  evals/
    <skill-name>/                       # Test scenarios and results
  .github/workflows/
    auto_release.yml                    # Tag-triggered build + GitHub release
    manual_release.yml                  # UI-triggered version bump + release
```

### Current Skills

| Skill | Directory | Description |
|-------|-----------|-------------|
| Trip Planner | `skills/trip-planner/` | Multi-week trip planning across 15+ sessions with cross-validated research, budget tracking, printable daily cards, and phase-based workflow |
| Obsidian Vault | `skills/obsidian-vault/` | Vault-aware editing for Obsidian. Detects conventions, understands Dataview, Tasks, Kanban, Meta Bind, and Templater interactions, and keeps notes connected, queryable, and safe. |

## Skill Architecture

Skills are discovered by `npx skills` scanning `skills/*/SKILL.md`. Each SKILL.md has YAML frontmatter with:

- `name` — must match parent directory, lowercase letters/numbers/hyphens
- `description` — explains what the skill does and when it should activate

The body contains the operational instructions: workflow steps, conventions, reference file loading triggers, and safe output rules.

Reference files (`references/*.md`) are loaded on demand — only when the agent enters a task that needs them. This keeps the core SKILL.md lean while having deep guidance available.

## Development Guidelines

- Each skill is self-contained in `skills/<name>/`
- No build system or package manager — skills are markdown files
- Follow existing skill structure when adding new skills
- SKILL.md frontmatter must have `name` and `description`
- Use the `trip-planner/` skill as a reference implementation

## Releasing

All skills share a single version in `package.json`. Two CI workflows handle releases:

- **`/release [patch|minor|major]`** (`.claude/commands/release.md`): Bumps version in `package.json`, commits, tags, and pushes. The Auto Release workflow then handles the build + GitHub release.
- **Manual Release** (`manual_release.yml`): Triggered from Actions UI. Pick patch/minor/major → bumps `package.json` → commits → tags → builds `.zip` archives → creates GitHub release.
- **Auto Release** (`auto_release.yml`): Triggered by `v*` tag push. Validates `package.json` version matches the tag → builds archives → creates GitHub release.

### Version file

Version lives in one place: `package.json` → `version`.

See `CONTRIBUTING.md` for full details.

## Adding a New Skill

1. Create `skills/my-skill/SKILL.md` with `name` and `description` in frontmatter
2. Add reference files under `skills/my-skill/references/` if needed
3. Update the root `README.md` skills table
