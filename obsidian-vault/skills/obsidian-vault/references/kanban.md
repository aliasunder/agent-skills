# Kanban Plugin Reference

The Kanban plugin renders markdown files as visual board views with draggable
cards organized into columns (lanes). The underlying file is plain markdown —
headings become lanes, list items become cards. This makes Kanban boards
programmatically editable, but the format is strict: malformed markdown breaks
the board view.

Read this before creating or editing any Kanban board file. An edit that looks
fine in source view can silently corrupt the board rendering.

**Official repo:** https://github.com/mgmeyers/obsidian-kanban

---

## File Structure

A Kanban board is a regular `.md` file with a specific frontmatter key and
a strict markdown structure:

```markdown
---

kanban-plugin: board

---

## Lane Name

- [ ] Card text
- [ ] Another card
- [x] Completed card


## Another Lane

- [ ] Card in second lane


## Archive

- [x] Archived card


%% kanban:settings
```
{"kanban-plugin":"board","key":"value"}
```
%%
```

### Required Elements

1. **Frontmatter:** Must contain `kanban-plugin: board` (or `kanban-plugin: basic`
   for the older format). This tells Obsidian to render the file as a board.
2. **Lanes:** Each `## Heading` becomes a column/lane on the board.
3. **Cards:** Each `- [ ] text` or `- text` list item under a lane becomes a card.
4. **Settings block:** A `%% kanban:settings ... %%` comment block at the very
   end of the file stores board configuration as JSON.

### Frontmatter Variants

```yaml
---
kanban-plugin: board       # Standard board view
---
```

The `board` value is the current standard. Older boards may use `basic`.
The Kanban plugin only reads `kanban-plugin` from frontmatter, but other
properties (like `tags`, `aliases`, or custom metadata) can coexist safely.
Adding `tags:` to a Kanban board file does not break the board — the plugin
ignores properties it doesn't recognize.

---

## Card Syntax

### Basic Cards

```markdown
- [ ] Simple task card
- [x] Completed card
- A card without a checkbox
```

Cards can have checkboxes (`- [ ]`, `- [x]`) or be plain list items (`- `).
Whether checkboxes show depends on the board's `show-checkboxes` setting.

### Cards with Dates

```markdown
- [ ] Card with due date @{2025-02-01}
- [ ] Card with date link [[2025-02-01]]
```

The `@{date}` syntax is Kanban-specific. The plugin can also parse dates from
wikilinks if `link-date-to-daily-note` is enabled in board settings.

### Cards with Metadata

Cards support Kanban-specific metadata using `<!-- -->` HTML comments or
content after the card text:

```markdown
- [ ] Card text ^card-id
```

Block IDs (`^id`) on cards are preserved and can be linked from other notes.

### Cards with Tasks Plugin Emoji Syntax

If the Tasks plugin is active and the board uses checkboxes, emoji syntax
on cards is parsed by the Tasks plugin:

```markdown
- [ ] Fix login bug 📅 2025-02-01 ⏫
- [ ] Write tests ⏳ 2025-01-28 📅 2025-02-01
```

This means:
- Tasks queries can find items from Kanban boards
- Toggling checkboxes in the board triggers Tasks plugin behavior
- Dates, priority, and recurrence work as documented in `references/tasks.md`

### Multi-line Cards

Cards can contain sub-items and additional content:

```markdown
- [ ] Main card text
	- Sub-item one
	- Sub-item two
	- [ ] Sub-task
```

Sub-items are indented with a tab or spaces under the parent card.

---

## Lane Structure

### Standard Lanes

Lanes are `##` headings. The order of headings determines the left-to-right
order of columns on the board:

```markdown
## Backlog
## In Progress
## Review
## Done
```

### Archive Lane

The Kanban plugin supports a special archive lane. Archived cards are moved
here (typically via the board UI). The archive lane:
- Is usually named `## Archive` but the name is configurable
- May or may not be visible in the board view
- Stores completed/archived cards with optional dates

When `archive-with-date` is enabled in settings, archived cards get a date
appended:

```markdown
## Archive

- [x] Old task @{2025-01-15}
```

### Blank Lines Between Lanes

The Kanban plugin expects **at least one blank line** between the last card of
one lane and the next `##` lane heading. Some boards use two blank lines
(the plugin's own serializer tends to add two), but one is sufficient for
correct rendering. The critical rule: do not place a `##` heading immediately
after a card line with no blank line between them — this can cause the board
to misrender or merge lanes.

```markdown
## Lane One

- [ ] Card


## Lane Two

- [ ] Another card
```

Removing ALL blank lines between a card and the next lane heading can cause
the board to merge lanes or misrender. When in doubt, preserve whatever
spacing the board already uses.

---

## Board Settings Block

Every Kanban board file ends with a settings block wrapped in an Obsidian
inline comment (`%% ... %%`):

```markdown
%% kanban:settings
```
{"kanban-plugin":"board","link-date-to-daily-note":false,"move-dates":true,"new-note-folder":"path/to/folder","show-checkboxes":true}
```
%%
```

**Critical rule:** Never modify the settings block structure. If you need to
edit a board, leave the `%% kanban:settings ... %%` block exactly as-is.

Common settings keys:
| Key | Type | Description |
|---|---|---|
| `kanban-plugin` | String | Board type (`"board"`) |
| `show-checkboxes` | Boolean | Whether to show checkboxes on cards |
| `link-date-to-daily-note` | Boolean | Whether dates link to daily notes |
| `move-task-metadata` | Boolean | Whether to update task metadata (dates) when cards are dragged between lanes. Some older boards may use `move-dates` instead — preserve whichever key the board already has. |
| `new-note-folder` | String | Folder for notes created from cards |
| `lane-width` | Number | Width of lanes in pixels |
| `show-relative-date` | Boolean | Show relative dates (e.g., "in 3 days") |

---

## Safe Editing Rules

When programmatically editing a Kanban board file:

### DO:
- Add new cards as `- [ ] text` list items under the appropriate lane heading
- Move cards between lanes by cutting from one lane's list and pasting under
  another lane's heading
- Edit card text in place
- Add or remove cards from any lane
- Preserve blank lines between lanes (two blank lines before each `##`)
- Preserve the settings block at the end of the file verbatim

### DO NOT:
- Change `##` headings without understanding they are lane names visible on
  the board
- Remove the `kanban-plugin: board` frontmatter
- Modify the `%% kanban:settings ... %%` block
- Add content between lanes that isn't a list item (no paragraphs, no
  sub-headings)
- Use `###` or deeper headings inside the board (the plugin only uses `##`)
- Remove the blank lines between the last card and the next lane heading
- Remove the `kanban-plugin` frontmatter key (other properties like `tags`
  are safe to add alongside it)

### Moving Cards Between Lanes

To move a card from one lane to another, remove the list item from the source
lane and add it to the target lane:

**Before:**
```markdown
## In Progress

- [ ] Task A
- [ ] Task B


## Done
```

**After (moving Task A to Done):**
```markdown
## In Progress

- [ ] Task B


## Done

- [x] Task A
```

Note: When moving to a "done" lane, you may want to change `[ ]` to `[x]` if
the board uses checkboxes and the task is being marked complete. Check the
board's convention.

---

## Interaction with Other Plugins

### Kanban ↔ Tasks Plugin

Since Kanban cards are standard markdown list items, the Tasks plugin can
read them. This creates a natural workflow:

- **Board columns as workflow stages** — Instead of using custom task statuses
  (`[/]` for "in progress"), some users use Kanban lane position to indicate
  status. A task in the "In Progress" lane is in progress by virtue of its
  lane, not its checkbox character.
- **Tasks queries across boards** — A Tasks query like
  `path includes TASKS.md` finds all tasks on the board.
- **Emoji metadata on cards** — Adding `📅 2025-02-01 ⏫` to card text
  makes it queryable by the Tasks plugin.

### Kanban ↔ Dataview

Dataview can query Kanban boards, but it sees the raw markdown — it doesn't
understand lane structure. Dataview treats each card as a list item or task.
To query cards by lane, you'd need to use `section` metadata or parse the
heading context.

### Kanban ↔ Meta Bind

Meta Bind INPUT fields placed in a Kanban board file may not render correctly
in the board view (the board renders its own UI, not standard markdown). Avoid
placing Meta Bind syntax inside Kanban boards.

### Agent Limitations for Column Moves

The Obsidian CLI can toggle a task checkbox (`obsidian task done path=
line=N`) and trigger Tasks plugin auto-dates, but it **cannot move a line
between `##` sections** — that requires a direct file edit. This means:

- **Completing a task by moving it to a Done lane** requires an Edit tool
  operation: remove the line from the source lane, add it under the target
  lane heading, and change `[ ]` to `[x]` if appropriate
- If the Tasks plugin's `setDoneDate` is enabled, the CLI toggle adds `✅`
  automatically — but the card stays in its current lane
- For full "move to Done + mark complete + add date", agents should do a
  direct file edit with all three changes in one pass

### New Note from Card

The Kanban plugin supports creating a linked note from a card via its
context menu ("New note from card"). The destination folder is set by the
`new-note-folder` setting in the board's settings block. When agents need
to split a verbose card into a card + linked note:

1. Create the detail note in the configured `new-note-folder` path
2. Replace the card's verbose content with a terse summary + a link to the
   detail note
3. Preserve any emoji metadata, block IDs, and checkbox state on the card

### Block IDs on Cards

Cards can have block IDs (`^card-id`) that make them linkable from other
notes:

```markdown
- [ ] Important task ^task-123
```

Block IDs are placed at the end of the card line (after any emoji metadata).
When editing cards, always preserve existing block IDs — other notes may
reference them with `[[Board#^task-123]]`.

### Combined Card Syntax (Description + Dates + Block ID)

When a card uses Tasks emoji dates AND a block ID, the ordering is strict:
**description → emoji dates (➕/✅) → block ID (`^id`)**. The block ID must
be the very last element on the line.

```markdown
- [ ] Fix login bug ➕ 2025-02-01 ^fix-login
- [x] Write tests ➕ 2025-01-28 ✅ 2025-02-01 ^write-tests
- [ ] Deploy v2.0 ⏫ ➕ 2025-02-01 📅 2025-02-15 ^deploy-v2
- [x] Ship release ⏫ ➕ 2025-02-01 ✅ 2025-02-10 ^ship-release
```

Wrong — block ID not at end, or non-emoji text after emoji fields:
```markdown
- [ ] Fix login bug ^fix-login ➕ 2025-02-01      ✗ block ID before dates
- [ ] Fix login bug ➕ 2025-02-01 ^fix-login oops  ✗ text after block ID
```

Obsidian requires block IDs at the end of the line. The Tasks plugin
recognizes and strips `^id` before parsing emoji fields backward (see
`references/tasks.md` → Common Pitfalls), so both plugins parse correctly
when the ordering is: description → emoji dates → block ID.

---

## Common Pitfalls

1. **Breaking blank lines** — Removing the blank lines between lanes causes
   the board to merge lanes or misrender. Always preserve `\n\n` between the
   last card and the next `##`.
2. **Editing the settings block** — The JSON in `%% kanban:settings ... %%`
   is the plugin's internal state. Editing it can break the board. Leave it
   alone.
3. **Sub-headings** — Using `###` inside a board creates unexpected rendering.
   The plugin only uses `##` for lanes.
4. **Mixed content** — Adding paragraphs, images, or code blocks between
   lanes (not as card content) confuses the board renderer.
5. **Frontmatter assumptions** — The Kanban plugin only reads `kanban-plugin`
   from frontmatter. Other properties (`tags`, `aliases`, custom keys) are
   safe — the plugin ignores them. Do not remove `kanban-plugin: board` or
   the board view breaks.
6. **Card indentation** — Sub-items must be indented with a tab or consistent
   spaces. Inconsistent indentation orphans sub-items.
