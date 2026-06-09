# Bases Reference

Bases are `.base` files that define database-like views over vault notes
(Obsidian 1.8+). They query frontmatter properties and display results as
tables, boards, or calendars — similar to Notion databases but over plain
markdown files.

Read this before creating or editing `.base` files, writing notes that feed
into a Bases view, or troubleshooting why notes aren't appearing in a view.

**Official docs:** https://help.obsidian.md/plugins/bases

---

## How Bases Work

1. A `.base` file defines a view configuration (source folder, columns,
   filters)
2. Bases reads all notes in the source folder/path
3. Each note becomes a row; frontmatter properties become columns
4. The view is interactive — clicking a cell edits the property in the
   source note

---

## Property Type Requirements

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

---

## Formulas

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

---

## Filters

Bases filters select which notes appear in the view. They go in the
top-level `filters:` key (all views) or view-level `filters:` (further
narrows). Filters use `and:` at the top level.

**Known working filter functions** (confirmed through trial — there is no
official documentation of available filter functions as of Obsidian 1.8.x):

| Function | Type | Example |
|---|---|---|
| `file.inFolder("path")` | File implicit | `file.inFolder("Career")` |
| `.containsAny("a", "b")` | String | `file.ext.containsAny("md", "pdf")` |

**`.is()` does not exist.** `file.ext.is("md")` and `type.is("session-log")`
both fail with "Cannot find function 'is' on type String". Use
`.containsAny()` instead:

```yaml
# ❌ Fails
- file.ext.is("md")
- type.is("session-log")

# ✅ Works
- file.ext.containsAny("md")
- type.containsAny("session-log")
```

**Unknown / untested:** `.eq()`, `.equals()`, `.startsWith()`,
`.endsWith()`, negation (`!` / `NOT`), OR logic at top level (only `and:`
confirmed).

---

## .base File Format

`.base` files are **YAML** (not markdown) — no frontmatter fences, no `---`
delimiters. Raw YAML starting at line 1.

```yaml
filters:
  and:
    - <filter expression>
properties:
  <property>:
    displayName: "Display Name"
views:
  - type: table          # table | board | cards | calendar
    name: "View Name"
    filters:
      and:
        - <filter expression>
    order:               # Columns (property names or file.* implicits)
      - title
      - file.mtime
    sort:
      - property: title
        direction: ASC   # ASC | DESC
    groupBy:
      property: file.folder
      direction: ASC
    columnSize:
      title: 280
```

**Implicit file properties** available as columns and in filters without
needing frontmatter: `file.name`, `file.ext`, `file.folder`, `file.mtime`,
`file.ctime`, `file.backlinks`, `file.embeds`, `file.file`.

---

## Views

| View | Description |
|---|---|
| Table | Spreadsheet-like table (default) |
| Board | Kanban-style board grouped by a property |
| Calendar | Calendar view based on a date property |

**Calendar view** requires a `date` key pointing to a date-type frontmatter
property:
```yaml
  - type: calendar
    date: date           # frontmatter property name
```

**Board view** requires `groupBy` on a categorical property. Notes without
the grouped property appear in a "No value" lane.

---

## Bases vs Dataview

| Scenario | Use |
|---|---|
| Browsable, interactive database view with click-to-edit | **Bases** |
| Dynamic computed content (progress bars, conditional formatting) | **DataviewJS** |
| Cross-folder queries with flexible filtering | **Dataview** (more filter functions available) |
| Mixed content dashboard (embeds + queries + static tables) | **Dataview + embeds** (Bases is one view per file) |

---

## Interaction with Other Plugins

**Bases ↔ Dataview:** Both read frontmatter. Same property consistency
requirements apply. If a property schema works for Dataview, it works for
Bases (not always vice versa — Bases is stricter).

**Bases ↔ Properties:** Both enforce type consistency. Properties pane shows
conflicts; Bases shows broken columns. They reinforce each other.

**Bases ↔ Meta Bind:** Meta Bind inputs modify frontmatter that Bases reads.
Changes propagate: user toggles a Meta Bind switch → frontmatter updates →
Bases view refreshes.
