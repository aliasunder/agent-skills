---
name: release
description: Bump version, commit, tag, and push to trigger the release workflow
arguments:
  - name: bump
    description: "Version bump type: patch, minor, or major"
    required: false
---

# Release

Bump the version across all plugin config files, commit, tag, and push. The CI release workflow handles building artifacts and creating the GitHub release.

## Steps

1. **Get bump type**: Use the `bump` argument if provided. Otherwise ask the user to choose: patch, minor, or major.

2. **Read current version** from `.claude-plugin/marketplace.json` field `metadata.version`.

3. **Calculate next version** by incrementing the chosen segment:
   - patch: `0.4.3` → `0.4.4`
   - minor: `0.4.3` → `0.5.0`
   - major: `0.4.3` → `1.0.0`

4. **Update all version files**:
   - `.claude-plugin/marketplace.json` → set `metadata.version` AND every `plugins[].version`
   - Find all `*/.claude-plugin/plugin.json` (excluding `./.claude-plugin/`) and update their `version`
   - `package.json` → set `version`
   - Use `jq` for JSON edits to preserve formatting

5. **Confirm with user**: Show the version change and list of files modified. Ask for confirmation before committing.

6. **Commit + tag + push**:
   - Stage all changed files
   - Commit with message: `release: vX.Y.Z`
   - Create tag: `vX.Y.Z`
   - Push commit and tag to origin
   - Do NOT add Co-Authored-By lines

7. **Report**: Tell the user the release is in progress and link to the Actions tab: `https://github.com/aliasunder/cowork-plugins/actions`
