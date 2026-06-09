# agent-skills

Carefully built skills for AI coding agents. Skills are packaged instructions that extend agent capabilities with specialized knowledge — install them and they activate automatically when relevant.

Works with Claude Code, GitHub Copilot, Cursor, Cline, and [many more](https://skills.sh).

## Installing

```bash
# Install all skills
npx skills add aliasunder/agent-skills

# Install a single skill
npx skills add aliasunder/agent-skills --skill obsidian-vault

# List available skills
npx skills add aliasunder/agent-skills --list
```

## Skills

| Skill | Description |
|-------|-------------|
| [trip-planner](skills/trip-planner/) | Plan a multi-week trip across 15+ sessions without losing context. Cross-validated research, budget tracking, printable daily cards, and a phase-based workflow from first idea to departure day. |
| [obsidian-vault](skills/obsidian-vault/) | AI that works with Obsidian, not just in it. Detects your vault's conventions, understands how Dataview, Tasks, Kanban, Meta Bind, and Templater interact, and creates or edits notes that are connected, queryable, and safe. |

## Works great with

If you use Obsidian, pair the **obsidian-vault** skill with [Vault Cortex](https://github.com/aliasunder/vault-cortex) for vault access via MCP. The skill teaches agents how to write correctly; Vault Cortex gives them the tools to read and write your vault from anywhere.

## Adding a skill

Each skill lives in its own directory under `skills/`:

```
skills/my-skill/
  SKILL.md              # Required — skill definition with name and description in frontmatter
  references/           # Optional — detailed reference docs loaded on demand
  assets/               # Optional — static files (dashboards, templates)
```

See any existing skill directory for a working example.

## License

[MIT](LICENSE)
