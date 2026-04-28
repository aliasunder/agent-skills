# Obsidian Properties Reference

Full reference for Obsidian frontmatter/properties — type rules, reserved keys,
edge cases, and common schemas. Read this before creating or repairing frontmatter,
or when designing a property schema for a vault folder or Bases source.

---

## Property Types

Obsidian's Properties pane (introduced in 1.4) infers property types from values.
Once a type is inferred for a property name vault-wide, Obsidian expects consistent
values for that property across all notes.

| Type | How to write it | Notes |
|---|---|---|
| **Text** | `key: "value"` or `key: value` | Quotes optional unless value contains special characters |
| **Number** | `key: 4.5` | Unquoted — quoting makes it text |
| **Checkbox** | `key: true` or `key: false` | Lowercase only — `True` / `False` are treated as text |
| **Date** | `key: 2025-01-15` | ISO 8601 date only |
| **Date & time** | `key: 2025-01-15T14:30:00` or `key: 2025-01-15T14:30:00-05:00` | ISO 8601 with time; timezone offset optional but recommended |
| **List** | YAML list (see below) | Multi-value property |
| **Link** | `key: "[[Note Name]]"` | Always quote wikilinks in properties |

**YAML list formats — all equivalent:**
```yaml
tags:
  - project
  - active

tags: [project, active]
```
Prefer the block format (indented list) for readability. The inline format
is valid but harder to read and edit.

---

## Reserved Property Keys

These keys have special meaning in Obsidian. Do not repurpose them for
other uses.

| Key | Type | Purpose |
|---|---|---|
| `tags` | List | Note tags — shown in Tags pane, searchable with `#tag` |
| `aliases` | List | Alternative names for the note — used in wikilink autocomplete |
| `cssclasses` | List | CSS class names applied to the note in reading view |
| `publish` | Checkbox | Obsidian Publish — controls whether the note is published |
| `permalink` | Text | Obsidian Publish — custom URL slug |

**`tags` rules:**
- Always a list — never `tags: project` (string)
- Values are bare strings — no `#` prefix in frontmatter (`tags: [project]` not `tags: [#project]`)
- Nested tags use `/`: `tags: [project/active]`
- Tags defined in frontmatter and inline `#tags` in the body are both valid
  and both appear in the Tags pane — pick one convention per vault and stick to it

**`aliases` rules:**
- Always a list
- Values are plain strings — not wikilinks
- Obsidian uses aliases in link autocomplete and `[[Note Name|alias]]` resolution

---

## Common Property Schemas

These are widely-used patterns. Adopt whichever fit the vault's purpose —
but be consistent once you start.

### Basic Note
```yaml
---
title: Note Title
date: 2025-01-15
tags:
  - topic
aliases:
  - Alt Name
---
```

### Project or Task Note
```yaml
---
title: Project Name
status: active           # active | paused | complete | archived
priority: high           # high | medium | low
due: 2025-03-01
created: 2025-01-10                  # or 2025-01-10T09:00:00-05:00 for full datetime with TZ
tags:
  - project
---
```

### People / Contact Note
```yaml
---
title: Full Name
role: colleague
org: Company Name
email: name@example.com
tags:
  - person
aliases:
  - First Name
---
```

### Daily Note
```yaml
---
date: 2025-01-15
tags:
  - daily
---
```

### Reference / Resource Note
```yaml
---
title: Resource Title
source: "https://example.com"
author: "[[Author Name]]"
published: 2025-01-01
read: false
tags:
  - resource
---
```

---

## Property Gotchas

**Wikilinks in property values must be quoted:**
```yaml
related: "[[Other Note]]"       ✓
related: [[Other Note]]         ✗  (breaks YAML parsing)
```

**Multi-value text vs list:**
```yaml
tags: project                   ✗  string — Dataview and Bases treat differently
tags:
  - project                     ✓  list — consistent behavior
```

**Numbers quoted become text:**
```yaml
rating: 4.5                     ✓  number — comparable in Dataview
rating: "4.5"                   ✗  text — won't sort numerically
```

**Booleans are case-sensitive in YAML:**
```yaml
published: true                 ✓  boolean
published: True                 ✗  string (in strict YAML parsers)
published: "true"               ✗  string
```

**Colons in values need quoting:**
```yaml
title: My Note: A Subtitle      ✗  breaks YAML
title: "My Note: A Subtitle"    ✓
```

**Multiline text values:**
```yaml
summary: |
  First line of summary.
  Second line of summary.
```

**Numeric-only tags are invalid:**
```yaml
tags:
  - 2025                            ✗  Obsidian rejects purely numeric tags
  - "2025"                          ✗  quoting doesn't help — still rejected
```
Use a separate frontmatter property for numeric values that tags can't hold:
```yaml
year: "2025"                        ✓  text property for the year
tags:
  - project                         ✓  descriptive tag
```

---

## Property Type Conflicts

If a property is defined as a number in one note but a string in another,
Obsidian's Properties pane shows a type conflict warning. This also causes
silent failures in Dataview comparisons and Bases filtering.

**How to find conflicts:**
- Open any note with the property → Properties pane shows a warning icon
- Dataview: query returns unexpected results (empty or wrong type comparisons)

**How to fix:**
- Decide on the canonical type
- Update all notes with that property to use the correct type
- If the vault uses a template, update the template first

When asked to add a new property to multiple notes, always check existing
notes in the vault to confirm the type convention before writing.
