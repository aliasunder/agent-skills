# Contributing

## Releasing

Two ways to cut a release — both end with a GitHub release and `.skill` artifacts attached.

### Option A: GitHub UI

1. Go to **Actions → Manual Release**
2. Click **Run workflow**
3. Pick `patch`, `minor`, or `major`
4. The workflow bumps versions, commits, tags, builds `.skill` artifacts, and creates the GitHub release

### Option B: Claude Code

Run `/release patch` (or `minor` / `major`). This bumps versions locally, commits, tags, and pushes. The **Auto Release** workflow picks up the tag and handles the build + GitHub release.

## How the CI works

| Workflow | File | Trigger | What it does |
|----------|------|---------|-------------|
| Manual Release | `manual_release.yml` | `workflow_dispatch` (Actions UI) | Bumps version in all JSON files → commits → tags → builds `.skill` zips → creates GitHub release |
| Auto Release | `auto_release.yml` | `v*` tag push | Validates all version files match the tag → builds `.skill` zips → creates GitHub release |

**Version validation**: Auto Release checks that every `plugin.json` and `marketplace.json` version matches the tag. If anything is out of sync, it fails with a clear error listing the mismatched files.

## Version files

All plugins share one version. These files must stay in sync:

| File | Fields |
|------|--------|
| `.claude-plugin/marketplace.json` | `metadata.version`, `plugins[].version` |
| `<plugin>/.claude-plugin/plugin.json` | `version` |
| `package.json` | `version` |

The CI handles this automatically. If you're bumping manually for some reason, update all of them.

## Adding a new plugin

1. Create `my-plugin/.claude-plugin/plugin.json` with the current version
2. Add skills under `my-plugin/skills/my-plugin/`
3. Add the plugin entry to `.claude-plugin/marketplace.json`

The CI auto-discovers plugins by finding `*/.claude-plugin/plugin.json` — no workflow edits needed.
