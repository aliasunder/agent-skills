# obsidian-vault

Claude that works *with* Obsidian, not just in it. This plugin teaches Claude how Obsidian works — frontmatter, link conventions, plugin syntax, and the interactions between them — so notes it creates or edits are connected, queryable, and structured the way your vault expects.

Without it, Claude writes markdown that looks fine but doesn't show up in your queries, breaks your boards, or uses the wrong link style.

## What you get

**Convention detection.** Every vault is different. On first use, the plugin reads your plugin configs and existing notes to detect link style, task format, frontmatter schemas, and tag conventions. It confirms what it finds and persists your conventions so Claude follows them consistently. No more re-explaining your setup every session.

**Vault-aware note design.** Claude considers how a note will be found and used in Obsidian: the right properties for your Dataview queries, links that connect in the graph, schemas that match sibling notes so Bases can display them. A note missing `status` doesn't show as "no status" in a query — it's just absent from results.

**Fluent in Obsidian's plugin ecosystem.** Dataview queries (with inline fields and the right date functions), Tasks plugin emoji syntax, Kanban board structure, Meta Bind inputs, Templater template delimiters, Bases property schemas, and Canvas JSON — all covered, including the non-obvious ways these plugins interact with each other. Detailed reference material loads on demand when the task calls for it.

**Safe output rules.** Before returning any note, the skill runs a checklist: frontmatter fenced and typed correctly, existing keys preserved, links in your vault's convention, block IDs intact, no silent type drift. You get the edit you asked for, not an accidentally restructured file.

## How it works

The skill triggers when Claude is working with `.md` files in a project that lives inside an Obsidian vault.

1. **Detect** — reads your vault's plugin configs and existing notes to identify conventions: link style, task format, frontmatter schemas, tag patterns
2. **Confirm** — surfaces what it found and checks with you before assuming anything
3. **Persist** — documents confirmed conventions in your project's CLAUDE.md so future sessions stay consistent
4. **Apply** — core conventions stay loaded for every interaction; plugin-specific references (Dataview syntax, Kanban structure, Templater functions) load on demand when needed

## Getting started

Install via npx:

```bash
npx skills add aliasunder/agent-skills --skill obsidian-vault
```

To start using it: just work with notes in your vault. The skill triggers whenever it detects an Obsidian vault in your project. The first time, the agent will read your plugin configs and existing notes, confirm your conventions with you, and persist everything — including a CLAUDE.md callout that keeps the skill active in future sessions. From there, every note the agent creates or edits follows your vault's patterns.

To skip the detection step and have the skill active from your very first interaction, add this to your project instructions:

```
This project lives inside an Obsidian vault. Always invoke the
obsidian-vault:obsidian-vault skill before working with any .md files.
```

## Pair with Vault Cortex

For the best experience, use this skill alongside [Vault Cortex](https://github.com/aliasunder/vault-cortex) — a remote MCP server that gives agents full access to your vault from anywhere. The skill teaches agents how to write correctly; Vault Cortex gives them the tools to read, search, and write your vault without requiring Obsidian to be running.

## Why a skill?

Obsidian isn't just markdown. Between Obsidian-flavored syntax, community plugin conventions, and the non-obvious ways plugins interact with each other, there's a lot of knowledge an agent needs to get your notes right.

Without the skill, that knowledge either lives in every project's CLAUDE.md or it doesn't exist at all. The skill carries it so your CLAUDE.md can focus on your project.

## Coverage

Reference documentation for the following, loaded on demand as needed:

- **Core plugins** — Properties, Bases, Canvas, Daily Notes, Templates, Graph
- **Community plugins** — Dataview, Tasks, Kanban, Meta Bind, Templater
- **Obsidian-flavored markdown** — embeds, block references, callouts, Mermaid, math
- **Property types** — YAML types, reserved keys, vault-wide type consistency
- **Vault access tools** — direct file ops, Obsidian CLI, MCP tools — when to use what

## Contributing

PRs welcome — especially for additional plugin conventions (Excalidraw, Obsidian Git, Periodic Notes, etc.) and cross-plugin interaction gotchas.

### File structure

```
skills/obsidian-vault/
  SKILL.md
  references/
    syntax.md
    properties.md
    core-plugins.md
    bases.md
    canvas.md
    dataview.md
    tasks.md
    kanban.md
    meta-bind.md
    templater.md
    vault-tools.md
```
