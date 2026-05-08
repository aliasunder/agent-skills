# Changelog


## [0.6.2] — 2026-05-08

### Documentation

- **obsidian-vault:** Add hash escaping gotcha and combined Kanban card example


## [0.6.1] — 2026-04-30

### Documentation

- Add CODEOWNERS for automatic PR review requests
- Add code of conduct, security policy, and PR template

### CI / Infrastructure

- Add package.json to version bump and validation workflows

### Maintenance

- Add package.json for registry metadata

### Other Changes

- Enhance security policy with scope and version details
- Delete SECURITY.md
- Update issue templates

All notable changes to this project are documented here. This marketplace
contains multiple plugins — changes are grouped by version with affected
plugins noted.

## [0.6.0] — 2026-04-30

### Features

- Reference updates, Bases/Canvas extraction, unified branding

### CI / Infrastructure

- Automated changelog, shared scripts, Node 24, plugin zips

### Other Changes

- Update AGENTS.md
- Update marketplace.json
- Update README.md
- Update README.md
## [0.5.3] — 2026-04-28

### trip-planner
- **Session log naming** — improved filename conventions for session logs
- **Task tool detection** — better detection of available task management
  tools in the environment

## [0.5.2] — 2026-04-28

### obsidian-vault
- **Comprehensive skill and reference improvements** — approach-aware
  convention detection (links, tags, daily notes, hub notes), proactive doc
  lookup principle, vault-tools.md reference, and fixes across all 8
  reference files (tasks, kanban, dataview, syntax, templater, core-plugins,
  properties, meta-bind)

## [0.5.1] — 2026-04-22

### obsidian-vault
- **Restructure plugin references** — convention detection system with
  persist-and-reuse workflow, new reference file loading pattern
- **Rewrite README** — updated plugin description and added skill invocation
  callout guidance

## [0.5.0] — 2026-04-21

### obsidian-vault (new plugin)
- **Initial release** — vault-aware editing skill with 9 reference files
  covering Obsidian-flavored markdown, properties, core plugins, Dataview,
  Tasks, Kanban, Meta Bind, Templater, and vault access tools
- Convention detection and persistence system
- Safe output checklist for render-correct notes
- Plugin ecosystem coverage (Dataview, Tasks, Kanban, Meta Bind, Templater,
  Bases, Canvas)

### CI
- Automated release workflows (tag-triggered and manual)
- Version validation across all plugin.json and marketplace.json files
- `/release` command for Claude Code

## [0.4.3] — 2026-04-07

### trip-planner
- Improved skill description for better auto-triggering

## [0.4.2] — 2026-04-06

### trip-planner
- Fixed skill activation and email check reliability

## [0.4.1] — 2026-04-02

### trip-planner
- Renamed from `trip-orchestrator` to `trip-planner`
- Improved CLAUDE.md scaffolding — agent working memory framing, file map
  with "when to read" triggers
- README rewrite for positioning

## [0.4.0] — 2026-04-02

### trip-planner
- Removed redundant commands — single orchestrator skill handles everything

## [0.3.1] — 2026-04-02

### trip-planner
- Fixed verbose preview step in session-end command

## [0.3.0] — 2026-04-02

### trip-planner
- Marketplace discovery — added marketplace manifest
- Quick-query eval suite (iteration 5) — 95% pass rate, confirmed no
  session bloat on simple lookups
- Added known-issues reference (escapeHtml dashboard bug)
- MIT license

## [0.2.0] — 2026-04-01

### trip-planner
- Strengthened materials-guide read prompting (eval 8 added)
- Star-preference nuance in hotel research
- Research file preservation rules
- MCP tools section for email integration

## [0.1.0] — 2026-03-31

### trip-planner (initial release)
- 8-phase trip planning workflow with session protocols
- Research methodology with cross-validation
- Budget tracking with multi-currency support
- Printable travel materials (daily cards, itinerary guides)
- 13-scenario eval suite
- Standalone kanban dashboard
