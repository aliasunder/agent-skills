---
name: release
description: Bump version, commit, tag, and push to trigger the release workflow
arguments:
  - name: bump
    description: "Version bump type: patch, minor, or major"
    required: false
---

# Release

Bump the version in package.json, commit, tag, and push. The CI release workflow handles building artifacts and creating the GitHub release.

## Steps

1. **Get bump type**: Use the `bump` argument if provided. Otherwise ask the user to choose: patch, minor, or major.

2. **Read current version** from `package.json` field `version`.

3. **Calculate next version** by incrementing the chosen segment:
   - patch: `1.0.0` → `1.0.1`
   - minor: `1.0.0` → `1.1.0`
   - major: `1.0.0` → `2.0.0`

4. **Update package.json**: Set `version` to the next version. Use `jq` for the edit to preserve formatting.

5. **Confirm with user**: Show the version change and the file modified. Ask for confirmation before committing.

6. **Commit + tag + push**:
   - Stage `package.json`
   - Commit with message: `release: vX.Y.Z`
   - Create tag: `vX.Y.Z`
   - Push commit and tag to origin
   - Do NOT add Co-Authored-By lines

7. **Report**: Tell the user the release is in progress and link to the Actions tab: `https://github.com/aliasunder/agent-skills/actions`
