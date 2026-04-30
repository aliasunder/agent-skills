# cowork-plugins

Carefully built plugins for [Cowork](https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork) and [Claude Code](https://code.claude.com/docs/en/overview). The plugin ecosystem is now shared — install in either environment and it shows up in both.

Install this marketplace via **Settings > Plugins > Add marketplace** using `aliasunder/cowork-plugins`.

## Plugins

| Plugin | Description |
|--------|-------------|
| [trip-planner](trip-planner/) | Plan a multi-week trip across 15+ sessions without losing context. Cross-validated research, budget tracking, printable daily cards, and a phase-based workflow from first idea to departure day. |
| [obsidian-vault](obsidian-vault/) | Claude that works with Obsidian, not just in it. Detects your vault's conventions, understands how Dataview, Tasks, Kanban, Meta Bind, and Templater interact, and creates or edits notes that are connected, queryable, and safe. |

## Installing

In the Claude desktop app:

1. Open **Settings > Plugins**
2. Click **Add marketplace**
3. Enter `aliasunder/cowork-plugins`
4. Select which plugins to enable

Each plugin has its own `.claude-plugin/plugin.json`, skills, and commands. Both Cowork and Claude Code discover them automatically.

## Adding a plugin

Each plugin lives in its own top-level directory with this structure:

```
my-plugin/
  .claude-plugin/plugin.json    # Required — name, version, description
  skills/my-skill/SKILL.md      # Skill definition
  commands/                     # Slash commands (optional)
  assets/                       # Static files (optional)
```

See any existing plugin directory for a working example.

## License

[MIT](LICENSE)
