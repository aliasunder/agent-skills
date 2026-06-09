# Dataview Plugin Reference

Dataview reads frontmatter properties, inline fields, and implicit metadata to
build dynamic views inside notes. Property names and types must be consistent
across all notes in a query set — inconsistency causes silent query failures,
not visible errors.

Read this before writing any Dataview query, inline field, or DataviewJS block.

**Official docs:** https://blacksmithgu.github.io/obsidian-dataview/

---

## Metadata Sources

Dataview indexes three categories of metadata:

### 1. Frontmatter Properties

All YAML frontmatter fields are automatically available as Dataview fields.
See `references/properties.md` for type rules.

Key rules for Dataview compatibility:

- Property names are **case-insensitive** in queries — `Status`, `status`, and
  `STATUS` all resolve to the same field. Match casing for readability but don't
  rely on it for uniqueness.
- Avoid special characters in property names — use hyphens, not spaces. If a key
  contains spaces, Dataview sanitizes it (lowercased, spaces → hyphens). Query
  using the sanitized form or `row["Original Name"]` syntax.
- Date properties must be valid **ISO 8601** for date comparisons to work:
  ```yaml
  due: 2025-02-01        ✓  (Dataview parses as date)
  due: Feb 1, 2025       ✗  (parsed as text — comparisons break)
  ```
- `tags` and `aliases` must be YAML lists. Dataview treats a bare string value
  as a single-item list, which causes unexpected query behavior with `contains()`.
- Nested YAML objects are queryable with dot notation:
  ```yaml
  thoughts:
    rating: 8
    reviewable: false
  ```
  Query: `WHERE thoughts.rating = 8`

### 2. Inline Fields

For properties that don't belong in frontmatter, use inline fields anywhere
in the note body. These are a Dataview-specific feature — Obsidian core does
not recognize them.

**Full-line inline fields** (field is the only content on the line):
```markdown
Rating:: 4
Author:: [[Jane Smith]]
Due:: 2025-02-01
Status:: active
```

**Inline within text** (field embedded in a sentence — use bracket syntax):
```markdown
I would rate this a [rating:: 9]! It was [mood:: acceptable].
```

**Hidden key** (key hidden in reading view — use parenthesis syntax):
```markdown
This will not show the (longKeyIDontNeedWhenReading:: key).
```

**Rules:**
- Double colon `::` with a space after — single colon is YAML, not Dataview
- Can appear anywhere in the note body, including in list items and tasks
- Dataview treats them the same as frontmatter properties in queries
- Links in inline fields don't need quoting (unlike frontmatter)
- For list items and tasks, always use the bracket `[key:: value]` syntax
  because the field is not the only content on the line
- Formatting tokens in keys (`**bold**`, `_italic_`) are stripped during
  indexing — query without them

**Override behavior for standalone fields:** If the same field name appears
as a standalone (full-line) inline field multiple times in a note, **the
last occurrence wins** — earlier values are silently overwritten. This is
different from list-item inline fields, which accumulate into a list.

### 3. Implicit Fields

Dataview provides metadata automatically without explicit annotation:

**Page-level implicit fields:**
| Field | Type | Description |
|---|---|---|
| `file.name` | Text | Filename without extension |
| `file.path` | Text | Full vault-relative path |
| `file.folder` | Text | Folder containing the file |
| `file.link` | Link | Clickable link to the file |
| `file.size` | Number | File size in bytes |
| `file.ctime` | Date | File creation time |
| `file.cday` | Date | File creation date |
| `file.mtime` | Date | Last modification time |
| `file.mday` | Date | Last modification date |
| `file.tags` | List | All tags (frontmatter + inline) |
| `file.etags` | List | Explicit tags only (no inherited sub-tags) |
| `file.inlinks` | List | Links pointing TO this file |
| `file.outlinks` | List | Links pointing FROM this file |
| `file.aliases` | List | Aliases from frontmatter |
| `file.tasks` | List | All tasks in the file |
| `file.lists` | List | All list items in the file |
| `file.frontmatter` | Object | Raw frontmatter as object |
| `file.day` | Date | Date derived from filename (if parseable) |
| `file.starred` | Boolean | Whether file is bookmarked |

**Task/list-item implicit fields:**
| Field | Type | Description |
|---|---|---|
| `text` | Text | Task/item text content |
| `completed` | Boolean | Whether task is checked |
| `fullyCompleted` | Boolean | Whether task and all subtasks are done |
| `status` | Text | Status character (`" "`, `"x"`, `"/"`, `"-"`, etc.) |
| `line` | Number | Line number in the file |
| `section` | Link | Link to the heading containing this item |
| `path` | Text | Path of the file containing the item |
| `link` | Link | Link to the nearest linkable block |
| `subtasks` | List | Subtasks of this item |
| `parent` | Number | Line number of the parent item (or null) |

---

## DQL Query Syntax

### Query Structure

```
<QUERY-TYPE> <fields>
FROM <source>
WHERE <condition>
SORT <field> ASC|DESC
GROUP BY <field>
FLATTEN <field>
LIMIT <number>
```

Only the query type is mandatory. Everything else is optional.

### Query Types

````markdown
```dataview
LIST
FROM #project
WHERE status = "active"
SORT date DESC
```

```dataview
TABLE status, tags, due
FROM "Projects"
WHERE due <= date(today) + dur(7 days)
SORT due ASC
```

```dataview
TASK
FROM #project
WHERE !completed
GROUP BY file.name
```

```dataview
CALENDAR due
FROM "Projects"
```
````

- **LIST** — bullet point list of pages (optionally with one field value)
- **TABLE** — table with one row per result, columns for specified fields
- **TASK** — interactive task list (renders checkboxes)
- **CALENDAR** — calendar view showing dots on dates from a date field

### FROM Sources

```
FROM "folder"              Folder path (vault-relative, in quotes, case-sensitive)
FROM "folder/subfolder"    Nested folder
FROM #tag                  Tag (matches tag and all sub-tags)
FROM #tag AND #other       Combine tags
FROM [[Note Name]]         Notes that link to this note
FROM outgoing([[Note]])    Notes that this note links to
FROM ""                    All notes in the vault
```

**Pitfall:** `FROM "folder"` is case-sensitive and must match the exact folder
path relative to vault root. `FROM "Projects"` ≠ `FROM "projects"`.

**Pitfall:** `FROM #tag` matches the tag AND all sub-tags. `#project` matches
both `#project` and `#project/active`.

### WHERE Conditions

```
WHERE status = "active"
WHERE due <= date(today)
WHERE contains(tags, "project")
WHERE file.name = "My Note"
WHERE !completed
WHERE rating > 3 AND status != "archived"
WHERE any(file.tags, (t) => t = "urgent")
WHERE length(file.outlinks) > 5
```

### SORT, GROUP BY, FLATTEN, LIMIT

```
SORT due ASC                      Sort ascending
SORT file.mtime DESC              Sort by modification time, newest first
GROUP BY file.folder              Group results by folder
GROUP BY status                   Group by property value
FLATTEN tags                      Expand list fields into separate rows
LIMIT 10                          Cap results
```

### Common Functions

**Date functions:**
- `date(today)` — today's date
- `date(now)` — current date and time
- `date("2025-01-15")` — parse date string
- `dur(1 day)`, `dur(7 days)`, `dur(1 week)`, `dur(2 months)` — durations
- `dateformat(date, "yyyy-MM-dd")` — format date

**String functions:**
- `contains(field, "value")` — check if field contains value
- `startswith(field, "prefix")`, `endswith(field, "suffix")`
- `replace(field, "old", "new")` — string replacement
- `upper(field)`, `lower(field)` — case conversion
- `regexmatch(field, "pattern")` — regex matching
- `length(field)` — length of string or list

**List functions:**
- `contains(list, "value")` — check if list contains value
- `length(list)` — number of items
- `any(list, (x) => condition)` — true if any item matches
- `all(list, (x) => condition)` — true if all items match
- `filter(list, (x) => condition)` — filter list items
- `map(list, (x) => expression)` — transform list items
- `flat(list)` — flatten nested lists
- `sort(list)` — sort list
- `join(list, separator)` — join list items into a string

**Object functions:**
- `default(field, fallback)` — use fallback if field is null/undefined
- `choice(condition, ifTrue, ifFalse)` — conditional value
- `typeof(field)` — return the type name

---

## Inline DQL Queries

Display a single computed value anywhere in a note using backtick syntax:

```markdown
Today is `= date(today)`.
This note has `= length(file.outlinks)` outgoing links.
Due date: `= this.due`
```

The default prefix is `=` (configurable in Dataview settings). Always produces
a single value — not a list or table.

---

## DataviewJS

For complex views beyond DQL's capabilities, DataviewJS provides full
JavaScript with the Dataview API:

````markdown
```dataviewjs
const pages = dv.pages("#project").where(p => p.status === "active");
dv.table(
  ["Name", "Status", "Due"],
  pages.map(p => [p.file.link, p.status, p.due])
);
```
````

**Common DataviewJS methods:**
- `dv.pages(source)` — query pages (same source syntax as FROM)
- `dv.page(path)` — get a single page by path
- `dv.current()` — the current page
- `dv.list(items)` — render a list
- `dv.table(headers, rows)` — render a table
- `dv.taskList(tasks, groupByFile)` — render tasks
- `dv.paragraph(text)` — render paragraph text
- `dv.header(level, text)` — render a heading
- `dv.span(text)` — render inline text
- `dv.el(element, text, attrs)` — render arbitrary HTML element

**Data array methods** (chainable, returned by `dv.pages()`):
- `.where(predicate)` — filter
- `.sort(field, direction)` — sort (`'asc'` or `'desc'`)
- `.groupBy(field)` — group (returns `{key, rows}` groups)
- `.map(fn)` — transform each item
- `.flatMap(fn)` — map and flatten
- `.limit(n)` — cap results
- `.array()` — convert to plain JS array
- `.values` — access underlying array

---

## Interaction with Other Plugins

### Dataview ↔ Tasks Plugin

These plugins both query tasks but work differently:

| Feature | Dataview TASK query | Tasks plugin query |
|---|---|---|
| Syntax | ```` ```dataview ```` | ```` ```tasks ```` |
| Reads emoji metadata | No — sees 📅, ⏳ etc. as literal text | Yes — parses natively |
| Filtering by due date | Must parse text manually | Built-in `due before`, `due after` |
| Completion tracking | Via `completed` implicit field | Via plugin's own tracking |
| Grouping/sorting | Full DQL expressions | Limited to built-in filters |
| Rendering | Interactive checkboxes if `taskCompletionTracking` is on (default); read-only if off | Interactive toggles (plugin's own tracking) |

**Recommendation:** Use Tasks plugin queries (`` ```tasks ```) for filtering
by dates, priority, and recurrence. Use Dataview TASK queries for aggregating
tasks across properties, folders, or complex expressions.

**Tip:** If both plugins are active, check Dataview's `taskCompletionTracking`
setting. When `false`, Dataview won't try to auto-complete tasks — avoiding
conflicts with the Tasks plugin's own completion tracking.

### Dataview ↔ Bases

Both read frontmatter. Bases is stricter about types:
- Property type conflicts (number in one note, string in another) break both,
  but Bases shows it as broken columns while Dataview silently returns wrong
  results.
- When designing schemas for folders that both Dataview and Bases will query,
  define types upfront and enforce with templates.
- Bases property names may be case-sensitive (unlike Dataview).
- **Bases ignores Dataview inline fields entirely.** Bases only reads YAML
  frontmatter properties — not `field:: value` inline fields. If data needs
  to appear in both Dataview queries and Bases views, put it in frontmatter.

### Dataview ↔ Meta Bind

Meta Bind INPUT fields modify frontmatter values. If a Meta Bind input changes
a property that a Dataview query reads, the query updates on the next Dataview
refresh cycle (configurable in Dataview settings, default 2.5 seconds).

---

## Common Pitfalls

1. **FROM path case sensitivity** — `FROM "Projects"` won't match a folder named
   `projects`. Always match exact case.
2. **Tag inheritance** — `FROM #project` also matches `#project/active`,
   `#project/archived`, etc. Use `WHERE contains(file.etags, "project")` for
   exact matching.
3. **String vs number** — `rating: "4"` (quoted) is text; `rating: 4` (unquoted)
   is a number. Comparisons like `WHERE rating > 3` fail silently on text values.
4. **Missing properties** — Notes missing a queried property return `null`, not
   an error. Use `WHERE field != null` or `default(field, fallback)`.
5. **Live preview lag** — Dataview doesn't update in real-time in edit mode.
   Switch to reading view or wait for the refresh interval (default 2.5s).
6. **Inline field on list items** — Must use bracket syntax `[key:: value]` when
   the field shares a line with other content.
7. **Dates from filenames** — `file.day` only works if the filename is or
   contains a parseable date (e.g., `2025-01-15` or `2025-01-15 Meeting`).
