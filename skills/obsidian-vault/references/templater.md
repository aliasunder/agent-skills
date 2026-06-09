# Templater Plugin Reference

Templater is a template engine that inserts dynamic content into notes using
`<% %>` delimiters. It replaces Obsidian's core Templates plugin with more
powerful scripting capabilities. Templates run once at insertion time —
Templater syntax in a non-template note renders as literal text, not computed
values.

Read this before writing Templater syntax or creating template files. Templater
syntax in the wrong context is a common source of confusion.

**Official docs:** https://silentvoid13.github.io/Templater/

---

## Core Concepts

### How Templater Works

1. Template files live in a designated **Templates folder** (configured in
   Templater settings)
2. When a template is inserted (via command, hotkey, or auto-trigger), Templater
   **processes all `<% %>` expressions** and replaces them with their output
3. The result is a normal note — no Templater syntax remains after processing
4. Templater syntax in non-template files is **not processed** and renders as
   literal text

### Templater vs Core Templates Plugin

Both can be active simultaneously. Key differences:

| Feature | Core Templates | Templater |
|---|---|---|
| Syntax | `{{title}}`, `{{date}}`, `{{time}}` | `<% tp.file.title %>`, `<% tp.date.now() %>` |
| Dynamic content | No — static placeholders only | Yes — JavaScript, conditionals, prompts |
| Folder templates | No | Yes — auto-apply template by folder |
| Trigger on creation | No | Yes — run template when new file is created |
| User prompts | No | Yes — `tp.system.prompt()`, `tp.system.suggester()` |
| File manipulation | No | Yes — rename, move, create files |

**When both are enabled:** Templater generally takes over template insertion
when invoked via its own commands. The core Templates plugin's folder setting
is still used by other plugins (e.g., Daily Notes references the core Templates
folder for its template path).

**Recommendation for agents:** When creating template files, use Templater
syntax (`<% %>`) if Templater is installed. Check for Templater by looking
for the `templater-obsidian` folder in `.obsidian/plugins/`.

---

## Template Syntax

### Expression Delimiters

| Delimiter | Purpose | Output |
|---|---|---|
| `<% expression %>` | Execute and insert result | Replaced with expression value |
| `<%* statement %>` | Execute without inserting | No output (side effects only) |
| `<%- expression %>` | Execute and insert raw (no escaping) | Replaced with raw value |

### Common Functions

**File functions (`tp.file`):**
```markdown
<% tp.file.title %>                     Note title (filename without .md)
<% tp.file.path() %>                    Full vault-relative path
<% tp.file.path(true) %>               Relative path from current folder
<% tp.file.folder() %>                  Parent folder name
<% tp.file.folder(true) %>             Full folder path
<% tp.file.creation_date("YYYY-MM-DD") %>  File creation date
<% tp.file.last_modified_date("YYYY-MM-DD") %>  Last modified date
<% tp.file.cursor() %>                  Place cursor here after insertion
<% tp.file.cursor(1) %>                 Numbered cursor position (tab order)
```

**Date functions (`tp.date`):**
```markdown
<% tp.date.now("YYYY-MM-DD") %>         Today's date
<% tp.date.now("YYYY-MM-DD", 7) %>      7 days from now
<% tp.date.now("YYYY-MM-DD", -1) %>     Yesterday
<% tp.date.now("dddd, MMMM Do YYYY") %> Friday, January 15th 2025
<% tp.date.tomorrow("YYYY-MM-DD") %>    Tomorrow
<% tp.date.yesterday("YYYY-MM-DD") %>   Yesterday
<% tp.date.weekday("YYYY-MM-DD", 1) %>  Next Monday
```

**Date format tokens** (Moment.js):
| Token | Output | Example |
|---|---|---|
| `YYYY` | 4-digit year | 2025 |
| `YY` | 2-digit year | 25 |
| `MM` | Month (zero-padded) | 01 |
| `M` | Month | 1 |
| `MMMM` | Month name | January |
| `MMM` | Month abbreviation | Jan |
| `DD` | Day (zero-padded) | 05 |
| `D` | Day | 5 |
| `dddd` | Day of week | Friday |
| `ddd` | Day abbreviation | Fri |
| `HH` | Hour (24h, zero-padded) | 14 |
| `mm` | Minute | 30 |
| `ss` | Second | 45 |

**System functions (`tp.system`):**
```markdown
<% await tp.system.prompt("Enter title") %>
```

Prompts the user for text input during template insertion.

```markdown
<% await tp.system.suggester(
  ["Active", "Archived", "Someday"],
  ["active", "archived", "someday"]
) %>
```

Shows a selection list. First array is display labels, second is return values.

```markdown
<% tp.system.clipboard() %>             Clipboard contents
```

**Web function (`tp.web`):**
```markdown
<% await tp.web.daily_quote() %>        Random daily quote
<% await tp.web.random_picture("200x200", "landscape") %>  Random image
```

### JavaScript in Templates

Full JavaScript is available between `<%* %>` delimiters:

```markdown
<%*
const title = await tp.system.prompt("Project name");
const slug = title.toLowerCase().replace(/ /g, "-");
await tp.file.rename(slug);
-%>
---
title: <% title %>
slug: <% slug %>
status: active
created: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - project
---

# <% title %>
```

**Key JavaScript features:**
- `await` is required for async functions (prompts, file operations)
- `tR +=` appends to the template result (useful in `<%* %>` blocks)
- Variables declared in one `<%* %>` block are accessible in later `<% %>` blocks
- The trailing `-%>` trims the trailing newline (prevents blank lines)

### Whitespace Control

| Syntax | Behavior |
|---|---|
| `<% expr %>` | Normal — preserves surrounding whitespace |
| `<% expr -%>` | Trims trailing newline |
| `<%- expr %>` | Raw output (no HTML escaping) |

Use `-%>` on `<%* %>` blocks that should produce no visible output (like
variable declarations):

```markdown
<%*
const status = "active";
-%>
Status: <% status %>
```

Without `-%>`, there would be a blank line before "Status:".

---

## Folder Templates

Templater can auto-apply templates to new files based on the folder they're
created in. Configured in Templater settings → Folder Templates.

When `trigger_on_file_creation` is enabled:
- New files in mapped folders automatically have the template applied
- This runs before the user types anything
- The template can prompt the user for input during creation

**Agent awareness:** If creating a new note in a folder with a folder template
configured, the template will run automatically. Do not manually add content
that the template would provide — it causes duplication.

### Sync and Cloud Storage Hazards

If the vault is stored on iCloud Drive, Dropbox, or another sync service,
`trigger_on_file_creation` can fire unexpectedly:

- **iCloud re-download:** When a file is evicted from local storage and
  re-downloaded, the OS creates it anew — Templater may treat this as a
  "new file" and re-run the template on an already-populated note
- **Sync conflict copies:** Sync services sometimes create conflict copies
  (e.g., `Note (Conflicted copy).md`). If these land in a mapped folder,
  Templater may process them
- **Cross-device creation:** A file created on one device triggers Templater
  when it syncs to another device with Obsidian running

These edge cases are hard to reproduce and easy to miss. If the vault uses
cloud sync AND `trigger_on_file_creation`, agents should be extra cautious
about creating files in mapped folders.

---

## Interaction with Other Plugins

### Templater ↔ Daily Notes

Daily Notes can be configured to use a template. If both core Templates and
Templater are installed, the template path in Daily Notes settings typically
points to the core Templates folder. When the daily note is created:
- Core Templates plugin handles simple `{{date}}` / `{{title}}` placeholders
- Templater processes `<% %>` syntax if `trigger_on_file_creation` is enabled

This means a daily note template can use Templater syntax and it will be
processed at creation time.

### Templater ↔ Tasks Plugin

Templates that create tasks should respect the vault's task format:
- If the vault uses Tasks emoji format, template tasks should include emoji
  syntax
- If `setCreatedDate` is enabled in Tasks settings, do NOT include `➕` in
  templates — the Tasks plugin adds it automatically
- Example template task:
  ```markdown
  - [ ] <% await tp.system.prompt("Task description") %> 📅 <% tp.date.now("YYYY-MM-DD", 7) %>
  ```

### Templater ↔ Meta Bind

Templater templates can contain Meta Bind inline code. Since Templater
processes `<% %>` syntax and Meta Bind uses backtick code blocks, they don't
interfere:

```markdown
Status: `INPUT[select(option(active), option(paused)):status]`
Created: <% tp.date.now("YYYY-MM-DD") %>
```

After Templater processes the file, the Meta Bind syntax remains as inline
code and becomes interactive.

### Templater ↔ Dataview

Templates can include Dataview query blocks. Since Dataview uses fenced code
blocks and Templater uses `<% %>`, they don't conflict. A template can
dynamically construct Dataview queries:

````markdown
```dataview
TABLE status, due
FROM "<% tp.file.folder(true) %>"
WHERE status = "active"
```
````

---

## Template File Best Practices

### Standard Template Structure

```markdown
<%*
const title = tp.file.title;
-%>
---
title: <% title %>
created: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - 
---

# <% title %>

<% tp.file.cursor() %>
```

### Conventions for Agents

When creating or editing template files:

1. **Only write Templater syntax in files inside the Templates folder.** Templater
   syntax in non-template notes is inert text.
2. **Use `tp.file.cursor()` for cursor placement** — tells Obsidian where to
   place the cursor after template insertion.
3. **Use `-%>` on script blocks** to prevent blank lines in output.
4. **Match the vault's date format** — check what format existing templates use.
5. **Include frontmatter** — templates should produce notes with frontmatter
   that matches the vault's property schemas (for Dataview/Bases compatibility).
6. **Don't hardcode the template folder path** — use `tp.file.folder()` or
   relative references.
7. **Beware the two-sources-of-truth problem** — If both Templater folder
   templates and agent workflows create notes in the same folders, there are
   two systems that think they're responsible for initial content. Document
   which system "owns" file creation in each folder and stick to it. If
   Templater owns a folder (via folder template mapping), agents should let
   Templater handle creation and only edit afterward. If agents own the
   folder, consider disabling the folder template mapping for it.

---

## Settings Awareness

Key Templater settings that affect agent behavior:

| Setting | Impact |
|---|---|
| `templates_folder` | Where template files live — do not create templates elsewhere |
| `trigger_on_file_creation` | If true, new files in mapped folders auto-run templates |
| `enable_system_commands` | If false, `tp.system.exec()` won't work |
| `enable_folder_templates` | If true, folder → template mappings are active |
| `syntax_highlighting` | Visual only — no functional impact |

---

## Common Pitfalls

1. **Templater syntax outside Templates folder** — `<% tp.date.now() %>` in a
   regular note is literal text, not a computed date. Only template files get
   processed.
2. **Missing `await`** — `tp.system.prompt("title")` without `await` returns
   a Promise object, not the user's input. Always `await` async functions.
3. **Blank lines from script blocks** — Use `-%>` to trim trailing newlines on
   `<%* %>` blocks that should produce no output.
4. **`trigger_on_file_creation` is a critical hazard for agent workflows** —
   When this setting is enabled, Templater runs a template on EVERY new file
   created in mapped folders, regardless of how the file was created. This
   means:
   - An agent creates a file with content → Templater detects the new file →
     runs the folder template → overwrites or duplicates the agent's content
   - The race condition depends on timing: if Templater fires before the
     agent finishes writing, the result is unpredictable
   - **Before creating any file in a vault with Templater installed:** check
     `.obsidian/plugins/templater-obsidian/data.json` for
     `trigger_on_file_creation` and `folder_templates` mappings
   - If the target folder has a mapping, either: (a) create the file and let
     the template handle initial content, then append/edit after; or (b) create
     the file in a different folder first, then move it
5. **Date format mismatch** — Templater uses Moment.js format tokens. ISO dates
   are `YYYY-MM-DD`, not `yyyy-MM-dd` (which is a different library's format).
6. **Template variables across blocks** — Variables from `<%* %>` blocks are
   available in later `<% %>` blocks within the same template, but not across
   different template files.
