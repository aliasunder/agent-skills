# Vault Access Tools Reference

Agents interact with Obsidian vaults through several tool tiers. Direct
file operations handle the vast majority of work — CLI and MCP tools are
optional supplements, not requirements.

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

### 3. Vault Cortex MCP (Works in Any Environment — Recommended When Available)

[Vault Cortex](https://github.com/aliasunder/vault-cortex) is an MCP server
that gives agents access to your vault without requiring Obsidian to be
running. Deploy it locally via Docker or remotely on a VPS — either way,
it works directly with vault files on disk. Vault Cortex delivers vault
content; the obsidian-vault skill ensures it renders correctly.

**Detection:** Look for `vault_*` tools (e.g. `vault_read_note`,
`vault_search`, `vault_write_note`) in the available tool list. MCP tools
can be easily marked as allowed in agent settings, unlike shell commands
which often require manual approval for each invocation.

**Capabilities:** Read, write, search (full-text and by tag/property/folder),
heading-targeted edits (append, prepend, replace within a section),
backlink and outgoing-link discovery, property management, and daily notes.

**When to prefer over direct file ops:**
- Full-text search (ranked by relevance, better than Grep for discovery)
- Property and tag queries across the vault
- Heading-targeted edits (no need to find and match text manually)
- Convention detection — search by property, list tags, and retrieve
  user preferences faster than scanning files manually

**When direct file ops are still better:**
- Bulk operations across many files (Grep + Edit in one pass)
- Creating files outside the vault
- Working with non-markdown files

### 4. Obsidian REST API + MCP Tools (Legacy Alternative)

The `obsidian-local-rest-api` community plugin exposes a REST API, and the
`mcp-tools` Obsidian plugin (or a dedicated MCP server) makes those
endpoints available as MCP tools.

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
  direct file ops and Vault Cortex genuinely can't do — requires a running
  Obsidian instance)
- Showing a file in Obsidian from a sandboxed session (where CLI isn't
  available)

---

## Environment Detection

At the start of a vault session, determine which tools are available:

| Check | How | Result |
|---|---|---|
| Direct file ops | Always available | ✓ primary tool |
| Vault Cortex | Look for `vault_*` tools in the tool list | Use for vault I/O when available |
| Obsidian CLI | Try `obsidian --version` in shell | Nice-to-have in Claude Code |
| REST API MCP | Check for `mcp__obsidian-mcp-tools__*` tools in the tool list | Nice-to-have for template execution |

If only direct file ops are available, that covers the vast majority of
vault work. Two operations require specific tools: opening a note in
Obsidian needs the CLI or REST API MCP (with Obsidian running locally),
and executing Templater templates needs the REST API MCP. If Vault Cortex
is available, use it for reads, writes, and searches — fall back to
direct file ops for bulk operations.

---

## Decision Matrix

| Task | Tool | Notes |
|---|---|---|
| Create a new note | Direct file ops | Vault Cortex: `vault_write_note` |
| Edit note content | Direct file ops | Vault Cortex: `vault_patch_note` for heading-targeted edits |
| Read a note | Direct file ops | Vault Cortex: `vault_read_note` |
| Search vault | Grep | Vault Cortex: `vault_search` (ranked by relevance) |
| Rename/move a note | Direct file ops (rename + Grep + Edit for links) | CLI `obsidian move` is a convenience, not a requirement |
| Create/complete tasks with dates | Direct file ops (write emoji dates directly) | — |
| Append to a heading | Direct file ops (find heading, insert after) | Vault Cortex: `vault_patch_note` |
| Open note in Obsidian | CLI `obsidian open` or REST API MCP | Only operation that requires CLI/MCP |
| Execute a Templater template | REST API MCP `execute_template` | Only operation that requires REST API MCP |

---

## Combining Tools

Some workflows benefit from combining tiers:

1. **Create + open:** Direct file ops to create the note, then CLI/MCP to
   open it in Obsidian for the user
2. **Search + edit:** Grep to find relevant notes, then direct file ops to
   read and edit them (or Vault Cortex `vault_search` + `vault_patch_note`)
3. **Bulk rename:** Direct rename of files + Grep to find all references +
   batch Edit to update links — handles hundreds of files in one pass,
   often faster than one-at-a-time CLI renames
