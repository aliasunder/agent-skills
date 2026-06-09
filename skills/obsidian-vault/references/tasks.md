# Tasks Plugin Reference

The Tasks plugin extends Obsidian's plain checkboxes with scheduling, priority,
recurrence, dependencies, and powerful query filters. Read this before writing
any Tasks plugin syntax — emoji-formatted tasks that look correct but violate
the plugin's parsing rules will silently fail to appear in queries.

**Official docs:** https://publish.obsidian.md/tasks/

---

## Task Formats

The Tasks plugin supports multiple task formats. The vault's active format is
configured in Tasks settings → Task Format. **Check which format the vault uses
before writing tasks** — mixing formats in the same vault causes query failures.

The most common format is **Tasks Emoji Format** (the default). This reference
covers the emoji format. Other formats (Dataview format, custom) exist but are
less common.

---

## Tasks Emoji Format

### Basic Syntax

```markdown
- [ ] Task description 📅 2025-02-01
- [ ] Task with priority ⏫ 📅 2025-02-01
- [ ] Scheduled task ⏳ 2025-01-28 📅 2025-02-01
- [x] Completed task ✅ 2025-01-30
- [-] Cancelled task ❌ 2025-01-30
```

### Date Emojis

| Emoji | Meaning | Description |
|---|---|---|
| 📅 | Due date | When the task is due |
| ⏳ | Scheduled date | When you plan to work on it |
| 🛫 | Start date | Earliest date the task can be started |
| ➕ | Created date | When the task was created |
| ✅ | Done date | When the task was completed |
| ❌ | Cancelled date | When the task was cancelled |

**Date format:** Always ISO 8601 (`YYYY-MM-DD`). No other formats are accepted.

**Auto-set dates:** The Tasks plugin can be configured to automatically add
created (➕), done (✅), and cancelled (❌) dates. Check the vault's Tasks
settings to see which are enabled. If auto-set is on, do not manually add
these emojis when creating tasks — the plugin will insert them.

**Tool-aware date handling:** How dates behave depends on which tool performs
the action:

| Action | Tool | Date behavior |
|---|---|---|
| Creating a task | Plugin modal / auto-suggest | Auto-adds ➕ if `setCreatedDate` is on |
| Creating a task | Direct file write (agent, editor) | No auto-date — add ➕ manually if wanted |
| Completing a task | Checkbox toggle in reading view | Auto-adds ✅ if `setDoneDate` is on |
| Completing a task | `obsidian task done path= line=` CLI | Auto-adds ✅ if `setDoneDate` is on |
| Completing a task | Direct file edit (agent moves to Done lane) | No auto-date — add ✅ manually |
| Cancelling a task | Status toggle to `[-]` in reading view | Auto-adds ❌ if `setCancelledDate` is on |
| Cancelling a task | Direct file edit (agent writes `[-]`) | No auto-date — add ❌ manually |

**Bottom line for agents:** When writing tasks directly to files, always
include the emoji dates you want — the plugin won't add them for you. When
the user will interact with the task through Obsidian's UI, check the
auto-set settings to avoid duplicates.

### Priority Emojis

| Emoji | Priority | Sort order |
|---|---|---|
| 🔺 | Highest | 0 (top) |
| ⏫ | High | 1 |
| 🔼 | Medium | 2 |
| (none) | Normal | 3 |
| 🔽 | Low | 4 |
| ⏬ | Lowest | 5 |

### Recurrence

```markdown
- [ ] Daily standup 🔁 every day 📅 2025-02-01
- [ ] Weekly review 🔁 every week on Friday 📅 2025-02-07
- [ ] Monthly report 🔁 every month on the 1st 📅 2025-02-01
- [ ] Biweekly sync 🔁 every 2 weeks 📅 2025-02-14
- [ ] Quarterly review 🔁 every 3 months 📅 2025-04-01
```

**Recurrence rules:**
- `🔁 every day`, `🔁 every week`, `🔁 every month`, `🔁 every year`
- `🔁 every week on Monday` (specific day of week)
- `🔁 every month on the 15th` (specific day of month)
- `🔁 every 2 weeks`, `🔁 every 3 months` (interval)
- `🔁 every week when done` (recurrence based on completion date, not due date)

When a recurring task is completed, the plugin creates a new instance with the
next occurrence date and marks the current one as done.

### Dependencies

| Emoji | Meaning | Usage |
|---|---|---|
| 🆔 | Task ID | Assigns a unique identifier to a task |
| ⛔ | Depends on | This task cannot start until the referenced task is done |

```markdown
- [ ] Set up database 🆔 db-setup
- [ ] Build API layer ⛔ db-setup 🆔 api-build
- [ ] Create frontend ⛔ api-build
```

Dependencies are checked by Tasks queries — a task with unmet dependencies can
be filtered with `is not blocked`.

### On Completion

The Tasks plugin supports an `🏁` (on completion) emoji for triggering actions
when a task is done. This is a newer feature — check plugin version before using.

---

## Custom Statuses

Beyond the default `[ ]` (todo) and `[x]` (done), the Tasks plugin supports
custom status characters. These are configured in Tasks settings.

**Common custom statuses:**
| Character | Typical meaning | Type |
|---|---|---|
| ` ` | Todo | TODO |
| `x` | Done | DONE |
| `/` | In Progress | IN_PROGRESS |
| `-` | Cancelled | CANCELLED |
| `>` | Deferred / Forwarded | TODO |
| `?` | Question | TODO |
| `!` | Important | TODO |

**Status types** determine behavior:
- **TODO** — counts as "not done" in queries, toggles to next status
- **IN_PROGRESS** — counts as "not done" but indicates active work
- **DONE** — counts as "done", adds completion date if auto-set is on
- **CANCELLED** — counts as "done" (for filtering purposes), adds cancelled date
- **NON_TASK** — not treated as a task at all

```markdown
- [ ] Not started yet
- [/] Currently working on this
- [x] Finished ✅ 2025-01-30
- [-] No longer needed ❌ 2025-01-30
```

**Important:** Custom statuses must be configured in the Tasks plugin settings
before they work. A `[/]` in a vault without "In Progress" configured is just
a plain checkbox with a `/` character — Tasks won't recognize it.

Each status has a `nextStatusSymbol` that determines what happens when the
checkbox is clicked (toggled). For example, clicking `[/]` might toggle to
`[x]` (done).

### Recurring Tasks and Custom Statuses

When a recurring task with a non-standard status (like `/` In Progress) is
completed by toggling to `[x]`, the new recurring instance gets the status
defined as the starting status in the cycle — typically `[ ]` (Todo).

---

## Tasks Query Blocks

````markdown
```tasks
not done
due before tomorrow
tags include #project
sort by due
```
````

### Query Filters

**Status filters:**
```
not done                          Excludes DONE and CANCELLED types
done                              Only DONE and CANCELLED types
status.type is TODO               Only TODO type
status.type is IN_PROGRESS        Only IN_PROGRESS type
status.name includes In Progress  Match by status name
```

**Date filters:**
```
due before tomorrow
due after 2025-01-01
due on 2025-02-01
due before today
due in this week
has due date
no due date
scheduled before today
starts before today
created after 2025-01-01
done after 2025-01-01
```

**Property filters:**
```
tags include #tag
tags do not include #tag
path includes folder/subfolder
path does not include archive
heading includes Section Name
description includes keyword
priority is high
priority is above medium
is recurring
is not recurring
is blocked                        Has unmet dependencies
is not blocked                    All dependencies met
```

**Combining filters:**
```
(due before tomorrow) OR (priority is high)
(tags include #urgent) AND (not done)
NOT (path includes archive)
```

### Query Sorting

```
sort by due
sort by due reverse
sort by priority
sort by status
sort by path
sort by description
sort by created
sort by done
sort by urgency                   Built-in urgency scoring
```

Multiple sort keys:
```
sort by priority
sort by due
sort by path
```

### Query Grouping

```
group by due
group by folder
group by filename
group by heading
group by status.name
group by tags
group by priority
group by happens                  Earliest of start/scheduled/due
```

### Query Display Options

```
hide due date
hide scheduled date
hide start date
hide created date
hide done date
hide cancelled date
hide priority
hide recurrence rule
hide edit button
hide postpone button
hide backlinks
hide toolbar
hide id
hide depends on
hide on completion
short mode                        Compact display
```

### Presets

The Tasks plugin supports user-defined presets — reusable filter snippets:

```
preset this_file                  Filter to current file only
preset this_folder                Filter to current folder
preset hide_everything            Hide all fields except description
```

Presets are configured in Tasks settings. Reference them by name with the
`preset` keyword. Common patterns:

```tasks
not done
preset this_file
sort by priority
```

### Query Limiting

```
limit 10                          Show at most 10 results
limit groups 3                    Show at most 3 groups
```

---

## Interaction with Other Plugins

### Tasks ↔ Kanban

Kanban cards that use checkbox syntax (`- [ ] Card text`) are recognized by the
Tasks plugin. This means:

- Tasks queries can find items from Kanban boards
- Toggling a card's checkbox in Kanban triggers Tasks plugin behavior (auto-dates,
  recurrence, status changes)
- If a Kanban board is used as a task board, the Tasks emoji syntax applies to
  card text

**Safe practice:** When editing a Kanban board file that also serves as a task
source, preserve the exact checkbox syntax and any emoji metadata.

### Tasks ↔ Dataview

See `references/dataview.md` → Interaction with Other Plugins → Dataview ↔ Tasks.

Key difference: Dataview TASK queries see emoji metadata as literal text.
Tasks plugin queries parse it natively. Use Tasks queries for date/priority
filtering; use Dataview for cross-property aggregation.

### Tasks ↔ Templater

Templates that create tasks should use the vault's configured task format.
If auto-created dates are enabled, templates should NOT include ➕ (created
date) — the plugin adds it automatically on creation. Including it manually
causes duplicate dates.

---

## Common Pitfalls

1. **Mixing task formats** — If the vault uses emoji format, don't write
   Dataview-style inline fields on tasks (`[due:: 2025-02-01]`). They won't
   be parsed by the Tasks plugin.
2. **Wrong date format** — `📅 01/02/2025` or `📅 February 1` won't parse.
   Always use `📅 YYYY-MM-DD`.
3. **When auto-set dates fire (and when they don't)** — `setCreatedDate`,
   `setDoneDate`, and `setCancelledDate` only trigger through the Tasks
   plugin's own UI: the task creation modal, checkbox toggle in reading
   view, or the Obsidian CLI `obsidian task done` command. **Direct file
   writes do not trigger auto-set dates** — whether by an agent, a text
   editor, or another plugin. This means agents can safely write emoji
   dates (➕, ✅, ❌) directly to files without causing duplicates. If
   auto-set dates are enabled and the user completes tasks through the
   plugin UI, the plugin adds the date — so agents should not add ✅ or
   ❌ dates when the task will be completed through the UI. But when
   agents are writing completed tasks directly to files (e.g., moving a
   card to Done in a Kanban board), they should include the emoji dates
   themselves because the plugin won't auto-add them.
4. **Unconfigured custom statuses** — Writing `- [/] In Progress` in a vault
   that hasn't configured the `/` status means Tasks treats it as a plain
   checkbox, not a custom status. Always check Tasks settings → Status Settings.
5. **Emoji ordering is a hard parsing rule** — The Tasks plugin reads
   backward from the end of the line to parse emoji fields. As soon as it
   encounters unrecognized text, it stops and ignores any emojis to the
   left. This means:
   - All emoji metadata MUST appear at the end of the task description,
     with no non-emoji text after them
   - `- [ ] Fix bug 📅 2025-02-01 some note` — the trailing text causes
     `📅` to be ignored
   - `- [ ] Fix bug ⏫ 📅 2025-02-01` ✓ — priority then date, both parsed
   - Recommended order: description text → priority → recurrence → created
     → start → scheduled → due → done/cancelled (but the key rule is: no
     non-emoji text after the first emoji field)
6. **Recurrence with custom statuses** — Completing a recurring task resets to
   the start of the status cycle, not the current status. A recurring `[/]`
   that's toggled to `[x]` creates a new `[ ]`, not a new `[/]`.
7. **Space after emoji** — Each emoji field needs a space before it:
   `📅 2025-02-01` ✓, `📅2025-02-01` ✗ (may not parse).
