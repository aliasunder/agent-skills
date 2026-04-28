# Core Plugins Reference

Obsidian's core plugins are built-in and don't require community installation.
They provide foundational features that other plugins build on. An agent
editing vault notes needs to understand which core plugins are active and how
they affect note behavior.

Read this before working with properties, daily notes, templates, canvas files,
or database views. Core plugin behavior shapes what "correct" looks like in
each vault.

**Official docs:** https://help.obsidian.md/plugins

---

## Properties

**Plugin ID:** `properties`

The Properties core plugin provides a visual sidebar pane for viewing and
editing frontmatter properties. It also manages **vault-wide property type
inference** — once a property name is used with a specific type in any note,
Obsidian expects that type everywhere.

### Property Types

| Type | YAML syntax | Notes |
|---|---|---|
| Text | `key: value` or `key: "value"` | Quotes required for special chars |
| Number | `key: 4.5` | Unquoted — quoting makes it text |
| Checkbox | `key: true` / `key: false` | Lowercase only |
| Date | `key: 2025-01-15` | ISO 8601 date |
| Date & time | `key: 2025-01-15T14:30:00` or `key: 2025-01-15T14:30:00-05:00` | ISO 8601 with time; timezone offset optional but recommended for cross-device consistency |
| List | YAML list | Multi-value property |
| Link | `key: "[[Note]]"` | Must be quoted |

### Type Inference and Vault-Wide Consistency

Obsidian infers property types vault-wide. If `status` is text in one note:
- All `status` properties in every note should be text
- Using `status: 3` (number) in another note creates a **type conflict**
- Type conflicts show warnings in the Properties pane
- Dataview and Bases queries may silently fail on conflicting types

**Agent rule:** Before adding a new property to a note, check if that property
name exists elsewhere in the vault. If it does, match the existing type. If
creating a new property name, choose the type deliberately — it sets the
convention vault-wide.

### Properties View Plugin

The Properties View core plugin (`properties`) adds the sidebar pane showing
all properties used across the vault. This is useful for:
- Discovering property names and types already in use
- Identifying type conflicts
- Understanding the vault's property schema

### Reserved Property Keys

These have special meaning in Obsidian core:

| Key | Type | Purpose |
|---|---|---|
| `tags` | List | Note tags — shown in Tags pane, searchable |
| `aliases` | List | Alternative names for link autocomplete |
| `cssclasses` | List | CSS classes applied in reading view |
| `publish` | Checkbox | Obsidian Publish — controls publishing |
| `permalink` | Text | Obsidian Publish — custom URL slug |

**Rules:**
- `tags` — always a list, bare strings (no `#` prefix), nested with `/`.
  **Numeric-only tags are invalid** — Obsidian rejects tags that are purely
  numbers (e.g., `2025`), even when quoted in YAML. Use a separate
  frontmatter property instead (e.g., `year: "2025"`).
- `aliases` — always a list, plain strings (not wikilinks)
- `cssclasses` — always a list, each item is a CSS class name

For comprehensive property rules and schemas, see `references/properties.md`.

---

## Daily Notes

**Plugin ID:** `daily-notes`

Creates one note per day with a consistent naming format and template.

### Configuration

| Setting | Description |
|---|---|
| Date format | Filename format (e.g., `YYYY-MM-DD`) — Moment.js tokens |
| New file location | Folder for daily notes |
| Template file location | Template applied to new daily notes |
| Open daily note on startup | Whether to auto-open today's note |

### Agent Awareness

- **Daily note folder:** Know where daily notes live to avoid creating files
  there accidentally. The folder is configured in settings.
- **Template:** Daily notes auto-apply a template on creation. If creating
  a daily note programmatically, the template runs automatically — don't
  duplicate its content.
- **Filename as date:** Plugins like Dataview can parse dates from daily note
  filenames (`file.day`). The date format must match what Dataview expects
  (ISO 8601 works best).
- **Link to daily notes:** Some plugins (e.g., Kanban with `link-date-to-daily-note`)
  create wikilinks to daily notes using the date format. Ensure consistency.
- **Plugin-managed daily notes:** Some community plugins (like
  `obsidian-list-modified`) automatically maintain sections within daily
  notes — e.g., logging files created, modified, or deleted that day. If
  such a plugin is active, **agents should not write directly to daily
  notes** to avoid races or content conflicts with the plugin. Check the
  vault's installed plugins and ask the user about daily note ownership
  before writing to them.

### Interaction with Templater

If both Daily Notes and Templater are active:
- The Daily Notes plugin creates the file and applies the template from core
  Templates settings
- Templater processes `<% %>` syntax in the template if
  `trigger_on_file_creation` is enabled
- This means daily note templates can use full Templater syntax

---

## Templates (Core)

**Plugin ID:** `templates`

Provides basic template insertion with static placeholders.

### Core Template Placeholders

```
{{title}}      Note title (filename without .md)
{{date}}       Current date (format from settings)
{{time}}       Current time (format from settings)
```

### Templates Folder

The template folder path is shared across plugins:
- Core Templates uses it for template insertion
- Daily Notes references it for its template path
- Templater has its own `templates_folder` setting (may differ)

### When Templater Is Also Installed

See `references/templater.md` → Templater vs Core Templates Plugin. Summary:
- Templater's syntax (`<% %>`) is more powerful than core placeholders
- Both can be active — Templater doesn't disable core Templates
- For template files, prefer Templater syntax when Templater is installed
- The core Templates folder setting still matters for Daily Notes integration

---

## Bases

**Plugin ID:** `bases` (Obsidian 1.8+)

Bases are `.base` files that define database-like views over vault notes. They
query frontmatter properties and display results as tables, boards, or
calendars — similar to Notion databases but over plain markdown files.

### How Bases Work

1. A `.base` file defines a view configuration (source folder, columns, filters)
2. Bases reads all notes in the source folder/path
3. Each note becomes a row; frontmatter properties become columns
4. The view is interactive — clicking a cell edits the property in the source note

### Property Type Requirements

Bases is **strict about property types** — stricter than Dataview:

| Bases column type | Required YAML type | Failure mode |
|---|---|---|
| Text | String | Works with most values |
| Number | Unquoted number | Quoted numbers show but don't sort/filter correctly |
| Checkbox | `true`/`false` | `"true"` (string) doesn't render as checkbox |
| Date | ISO 8601 | Non-ISO dates show as text, don't filter by date |
| Multi-select | YAML list | Comma-separated strings don't split into tags |
| Link | Quoted wikilink | Unquoted wikilinks break YAML |

**Agent rule:** When writing notes that will appear in a Bases view, ensure
every property matches the expected type exactly. Test by checking existing
notes in the same source folder.

**Inline field limitation:** Bases only reads YAML frontmatter properties.
Dataview inline fields (`field:: value` in the note body) are invisible to
Bases. If data needs to appear in a Bases view, it must be in frontmatter.

### Bases Formulas

Bases supports formulas for computed columns:

```
prop("due")                      Reference a property
now()                            Current date
if(prop("done"), "✅", "⬜")     Conditional
dateAdd(prop("due"), 7, "days")  Date arithmetic
```

**Common formula functions:**
- `prop("name")` — get property value
- `now()`, `today()` — current date/time
- `if(condition, then, else)` — conditional
- `dateAdd(date, amount, unit)` — date arithmetic
- `dateBetween(date1, date2, unit)` — difference between dates
- `contains(text, search)` — text search
- `length(list)` — list length
- `empty(value)` — check if empty/null
- `format(value, pattern)` — format values
- `slice(text, start, end)` — substring
- `concat(a, b, ...)` — concatenate strings

### Bases Views

| View | Description |
|---|---|
| Table | Spreadsheet-like table (default) |
| Board | Kanban-style board grouped by a property |
| Calendar | Calendar view based on a date property |

### Interaction with Other Plugins

**Bases ↔ Dataview:** Both read frontmatter. Same property consistency
requirements apply. If a property schema works for Dataview, it works for
Bases (not always vice versa — Bases is stricter).

**Bases ↔ Properties:** Both enforce type consistency. Properties pane shows
conflicts; Bases shows broken columns. They reinforce each other.

**Bases ↔ Meta Bind:** Meta Bind inputs modify frontmatter that Bases reads.
Changes propagate: user toggles a Meta Bind switch → frontmatter updates →
Bases view refreshes.

---

## Canvas

**Plugin ID:** `canvas`

Canvas provides an infinite whiteboard for spatial organization of notes,
text, images, links, and groups. Canvas files are **JSON** (`.canvas`), not
markdown.

### Canvas File Format

Canvas uses the [JSON Canvas](https://jsoncanvas.org/) open spec:

```json
{
  "nodes": [
    {
      "id": "unique-id",
      "type": "text",
      "text": "Node content",
      "x": 0, "y": 0,
      "width": 250, "height": 60
    }
  ],
  "edges": [
    {
      "id": "edge-id",
      "fromNode": "node-a",
      "fromSide": "right",
      "toNode": "node-b",
      "toSide": "left"
    }
  ]
}
```

### Node Types

| Type | Key fields | Description |
|---|---|---|
| `text` | `text` | Free text content (supports markdown) |
| `file` | `file` | Embedded note (vault-relative path) |
| `link` | `url` | Embedded web link |
| `group` | `label` | Visual grouping container |

### Edge Properties

| Field | Values | Required |
|---|---|---|
| `id` | Unique string | Yes |
| `fromNode` | Node ID | Yes |
| `fromSide` | `top`, `right`, `bottom`, `left` | Yes |
| `toNode` | Node ID | Yes |
| `toSide` | `top`, `right`, `bottom`, `left` | Yes |
| `color` | Color string | No |
| `label` | Edge label text | No |

### Node Color and Style

Nodes can have colors:
```json
{
  "id": "node-1",
  "type": "text",
  "text": "Important",
  "color": "1",
  "x": 0, "y": 0,
  "width": 250, "height": 60
}
```

Color values: `"1"` through `"6"` (maps to Obsidian's color palette), or
hex colors like `"#ff0000"`.

### Rules

- Every node and edge needs a **unique `id`** — use random strings, not
  sequential integers
- Edge `fromNode` and `toNode` must reference valid node IDs
- `file` nodes use vault-relative paths: `"file": "Notes/My Note.md"`
- `link` nodes use URLs: `"url": "https://example.com"`
- Groups contain other nodes spatially (by overlapping coordinates), not by
  reference
- Do not attempt to read or write `.canvas` files as plain text — always
  parse/generate as JSON

### Enhanced Canvas (Community Plugin)

The Enhanced Canvas community plugin adds:
- **Frontmatter on canvas nodes** — text nodes can have YAML frontmatter
- **Custom CSS on canvas** — CSS snippets for canvas styling
- **Additional node types and behaviors**

When Enhanced Canvas is active and `enableFrontmatter` is true, text nodes
can include frontmatter:

```json
{
  "id": "node-1",
  "type": "text",
  "text": "---\nstatus: active\ntags:\n  - project\n---\n\nNode content here"
}
```

---

## Graph View

**Plugin ID:** `graph`

The Graph view visualizes connections between notes as an interactive node
graph. Connections are derived from links (wikilinks and markdown links).

### Agent Relevance

- **Link convention affects the graph.** Both wikilinks and markdown links
  create connections. The graph doesn't distinguish between link types.
- **Orphan notes** (no incoming or outgoing links) appear as disconnected
  nodes — this may or may not be desirable depending on the vault's structure.
- **Tags do not create graph connections** — only links between notes do.
- **Aliases** affect link resolution but not graph structure.

### Local Graph

Each note has a local graph showing its direct connections. This is useful
for understanding a note's context without seeing the full vault graph.

---

## Backlinks

**Plugin ID:** `backlink`

Shows all notes that link TO the current note. This is the reverse of outgoing
links.

### Agent Relevance

- Creating a link to a note makes it appear in that note's backlinks pane
- Removing a link removes it from backlinks
- Both wikilinks and markdown links create backlinks
- Unresolved links (pointing to nonexistent notes) still appear in backlinks
  as "unlinked mentions"

---

## Outgoing Links

**Plugin ID:** `outgoing-link`

Shows all notes that the current note links TO.

---

## Note Composer

**Plugin ID:** `note-composer`

Provides commands to merge notes and extract sections into new notes.

### Agent Awareness

- **Extract** — splits a selected section into a new note and replaces it
  with a link
- **Merge** — combines two notes into one
- These operations update links automatically — but only within the vault
- Agents should not manually simulate merge/extract operations — they risk
  breaking links. Recommend the user use Note Composer's built-in commands.

---

## Bookmarks

**Plugin ID:** `bookmarks`

Allows users to bookmark notes, headings, searches, and graphs for quick
access.

### Agent Relevance

- Bookmarks are stored in `.obsidian/bookmarks.json` — not in note frontmatter
- `file.starred` implicit field in Dataview reflects bookmark status
- Agents don't need to manage bookmarks directly

---

## Slides

**Plugin ID:** `slides`

Renders notes as presentation slides using `---` (horizontal rules) as slide
separators.

### Agent Awareness

When creating a note intended for slide presentation:
- Separate slides with `---` (three hyphens on their own line)
- Each slide is the content between two `---` separators
- Use standard markdown formatting (headings, lists, images)
- The first `---` after frontmatter starts the first slide — don't confuse
  frontmatter fences with slide separators

```markdown
---
title: My Presentation
---

# Slide One

Content here.

---

# Slide Two

More content.

---

# Slide Three

Final thoughts.
```

**Pitfall:** If a note has frontmatter and uses slide separators, ensure the
frontmatter is properly fenced (`---` ... `---`) before the first slide content.
The Slides plugin distinguishes frontmatter fences from slide separators by
position (frontmatter must be at the very top of the file).

---

## Sync

**Plugin ID:** `sync`

Obsidian Sync synchronizes vault content across devices.

### Agent Relevance

- Sync may cause brief file conflicts if the same file is edited on multiple
  devices simultaneously
- Agents editing files during active sync should expect `.obsidian/` config
  to change between reads
- Large batch operations (editing many files rapidly) may temporarily lag sync
- `.obsidian/` folder content (plugin settings, workspace state) is synced —
  changes to plugin configs propagate across devices

---

## File Recovery

**Plugin ID:** `file-recovery`

Maintains snapshots of file changes for recovery.

### Agent Relevance

- File Recovery provides a safety net for destructive edits
- Agents making large changes to many files can reassure users that File
  Recovery provides rollback capability
- Snapshots are stored locally, not in the vault directory

---

## Other Active Core Plugins

These are commonly active but have minimal impact on agent behavior:

| Plugin | Purpose | Agent impact |
|---|---|---|
| File Explorer | Sidebar file browser | None — agent uses file tools directly |
| Global Search | Full-text search | None — agent uses Grep/Glob |
| Quick Switcher | File switcher dialog | None |
| Tag Pane | Tag browser sidebar | None — tags are in frontmatter/inline |
| Outline | Table of contents sidebar | None |
| Word Count | Status bar word count | None |
| Command Palette | Command search | None |
| Editor Status | Status bar info | None |
| Workspaces | Save/load UI layouts | None |
| Page Preview | Hover preview on links | None |
| Footnotes | Footnote support | See `references/syntax.md` |
