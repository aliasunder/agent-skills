# Vault Access Tools Reference

Agents interact with Obsidian vaults through three tool tiers. Direct file
operations handle the vast majority of work — CLI and MCP tools are optional
supplements for niche situations, not requirements.

---

## Tool Tiers

### 1. Direct File Operations (Always Available — Primary Tool)

Read, Write, and Edit tools work on `.md` files directly. These are the
primary tool for virtually all vault work — available in every agent
environment (Claude Code, Cowork, Cursor, etc.).

**Capabilities:**
- Read any file in the vault
- Create new notes with frontmatter, content, and links
- Edit existing notes (surgical string replacement)
- Full control over file content and structure
- Rename/move files and batch-update links via Grep + Edit (works well
  for both single files and bulk operations — hundreds of renames and link
  updates in one pass)
- Write Tasks plugin emoji dates (➕, ✅, ❌, etc.) directly — produces
  identical results to what the plugin's own UI writes
- Append to any section by finding the heading and inserting after it

**Limitations:**
- No awareness of Obsidian's runtime state (which note is open, sidebar
  state, etc.)
- Cannot trigger plugin UI behaviors (Templater processing on file creation,
  Tasks plugin auto-date via checkbox toggle) — but this rarely matters in
  practice since agents write the same content directly
- Rename/move requires manually finding and updating links in other files
  (via Grep + Edit), whereas Obsidian's own rename updates links
  automatically — but the manual approach is equally effective and often
  faster for bulk operations

**When to use:** Default for everything. Most agent sessions use direct
file ops exclusively and never need CLI or MCP.

### 2. Obsidian CLI (Local Shell Access Only)

The Obsidian CLI (`obsidian` command) provides a bridge to the running
Obsidian app. **Requires Obsidian to be running on the same machine** and
is only available in environments with direct shell access to the user's
system (Claude Code in a local terminal, other terminal-based agents). Not
available in sandboxed environments (Cowork's shell runs in a sandbox
without access to the user's Obsidian instance).

**Capabilities:**
- `obsidian open <path>` — open a note in Obsidian
- `obsidian search <query>` — full-text search the vault
- `obsidian move <from> <to>` — rename/move with automatic link updates
- `obsidian task done path=<path> line=<N>` — toggle task checkbox
  (triggers Tasks plugin auto-dates if configured)
- `obsidian show-file <path>` — reveal file in Obsidian

**Limitations:**
- Only available when Obsidian is running locally
- Not available in sandboxed environments (Cowork's shell has no access to
  user's Obsidian)
- Single-file operations only — for bulk work, scripted direct file ops
  are faster

**When to use:**
- Opening a specific note in Obsidian for the user
- Single-file rename when you want zero-effort link propagation (but
  direct file ops + Grep work just as well)
- Full-text search as a complement to Grep

### 3. Obsidian REST API + MCP Tools (Works in Any Environment)

The `obsidian-local-rest-api` community plugin exposes a REST API, and the
`mcp-tools` Obsidian plugin (or a dedicated MCP server) makes those
endpoints available as MCP tools. This works in both Cowork and Claude Code,
and is the path for the few operations that genuinely need Obsidian
interaction beyond direct file ops.

**Requirements:**
- `obsidian-local-rest-api` plugin installed and enabled in Obsidian
- MCP connector configured (either `mcp-tools` plugin or standalone server)
- Obsidian must be running

**Capabilities (via `mcp__obsidian-mcp-tools__*` tools):**
- Read and write vault files through Obsidian's API
- Search the vault (simple text search, smart search)
- Append to files, patch files (find-and-replace through the API)
- Execute templates
- Show files in Obsidian

**Limitations:**
- Requires two plugins to be installed and configured
- API must be running (Obsidian must be open)
- Some operations are slower than direct file ops

**When to use:**
- Triggering Templater template execution programmatically (the one thing
  direct file ops genuinely can't do)
- Showing a file in Obsidian from a sandboxed session (where CLI isn't
  available)
- Searching the vault when Grep isn't sufficient (smart/semantic search)

---

## Environment Detection

At the start of a vault session, determine which tools are available:

| Check | How | Result |
|---|---|---|
| Direct file ops | Always available | ✓ primary tool |
| Obsidian CLI | Try `obsidian --version` in shell | Nice-to-have in Claude Code |
| MCP tools | Check for `mcp__obsidian-mcp-tools__*` tools in the tool list | Nice-to-have for template execution |

If only direct file ops are available, that covers the vast majority of
vault work. The main things you genuinely can't do without CLI/MCP are:
opening notes in Obsidian for the user and executing Templater templates
programmatically.

---

## Decision Matrix

| Task | Tool | Notes |
|---|---|---|
| Create a new note | Direct file ops | — |
| Edit note content | Direct file ops | — |
| Read a note | Direct file ops | — |
| Search vault | Grep | CLI/MCP search available as supplements |
| Rename/move a note | Direct file ops (rename + Grep + Edit for links) | CLI `obsidian move` is a convenience for single files, not a requirement |
| Create/complete tasks with dates | Direct file ops (write emoji dates directly) | Plugin auto-dates via CLI are redundant when agents write the dates themselves |
| Append to a heading | Direct file ops (find heading, insert after) | — |
| Open note in Obsidian | CLI `obsidian open` or MCP `show_file_in_obsidian` | Only operation that requires CLI/MCP |
| Execute a Templater template | MCP `execute_template` | Only operation that requires MCP |

---

## Combining Tools

Some workflows benefit from combining tiers:

1. **Create + open:** Direct file ops to create the note, then CLI/MCP to
   open it in Obsidian for the user
2. **Search + edit:** Grep to find relevant notes, then direct file ops to
   read and edit them
3. **Bulk rename:** Direct rename of files + Grep to find all references +
   batch Edit to update links — handles hundreds of files in one pass,
   often faster than one-at-a-time CLI renames
