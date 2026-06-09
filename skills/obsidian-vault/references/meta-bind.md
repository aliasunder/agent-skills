# Meta Bind Plugin Reference

Meta Bind creates interactive input fields, metadata displays, and buttons
inside notes. Input fields bind to frontmatter properties with two-way sync —
changing the input updates the property, and changing the property updates the
input. This makes notes interactive without leaving reading/live-preview mode.

Read this before writing any Meta Bind syntax. Malformed declarations render
as error blocks. More importantly, INPUT fields that bind to frontmatter
properties create a live editing channel — agents that manually edit the same
properties risk desync.

**Official docs:** https://mprojectscode.github.io/obsidian-meta-bind-plugin-docs/

---

## Core Concepts

Meta Bind has three element types:

| Element | Purpose | Syntax prefix | Modifies frontmatter? |
|---|---|---|---|
| **INPUT** | Interactive input bound to a property | `INPUT[` | Yes — two-way binding |
| **VIEW** | Read-only display of a property value | `VIEW[` | No — display only |
| **BUTTON** | Clickable button that triggers actions | `BUTTON[` | Depends on action |

All three can be written as **inline code** (backtick) or **code blocks**
(fenced). Inline is more common for embedding in text.

---

## INPUT Fields

INPUT fields create interactive form elements bound to frontmatter properties.

### Inline Syntax

```markdown
`INPUT[input_type:property_name]`
```

### Code Block Syntax (for complex configurations)

````markdown
```meta-bind
INPUT[input_type:property_name]
```
````

### Input Types

| Type | Renders as | Value type | Example |
|---|---|---|---|
| `text` | Text input | String | `INPUT[text:title]` |
| `textArea` | Multi-line text area | String | `INPUT[textArea:notes]` |
| `number` | Number input | Number | `INPUT[number:rating]` |
| `slider` | Range slider | Number | `INPUT[slider(minValue(1), maxValue(10)):rating]` |
| `toggle` | Toggle switch | Boolean | `INPUT[toggle:done]` |
| `date` | Date picker | Date (ISO) | `INPUT[date:due]` |
| `dateTime` | Date+time picker | DateTime | `INPUT[dateTime:scheduled]` |
| `select` | Dropdown select | String | `INPUT[select(option(a), option(b)):status]` |
| `multiSelect` | Multi-select pills | List | `INPUT[multiSelect(option(a), option(b)):tags]` |
| `suggester` | Searchable dropdown | String | `INPUT[suggester(option(a), option(b)):category]` |
| `editor` | Rich text editor | String | `INPUT[editor:content]` |
| `imageSuggester` | Image file picker | String | `INPUT[imageSuggester:cover]` |
| `progressBar` | Progress bar | Number | `INPUT[progressBar:progress]` |
| `inlineSelect` | Inline dropdown | String | `INPUT[inlineSelect(option(a), option(b)):status]` |
| `listSuggester` | Suggest from a list | String | `INPUT[listSuggester(optionQuery("path/folder")):related]` |
| `inlineList` | Editable inline list | List | `INPUT[inlineList:items]` |
| `inlineListSuggester` | List with suggestions | List | `INPUT[inlineListSuggester(optionQuery("")):tags]` |

### Arguments

Arguments are specified in parentheses after the input type:

```markdown
`INPUT[slider(minValue(1), maxValue(10), addLabels):rating]`
`INPUT[select(option(active), option(paused), option(complete)):status]`
`INPUT[text(placeholder(Enter title here)):title]`
```

**Common arguments:**
| Argument | Input types | Description |
|---|---|---|
| `minValue(n)` | slider, number, progressBar | Minimum value |
| `maxValue(n)` | slider, number, progressBar | Maximum value |
| `option(value)` | select, multiSelect, suggester | Add a selectable option |
| `option(value, label)` | select, multiSelect, suggester | Option with display label |
| `placeholder(text)` | text, textArea, number | Placeholder text |
| `addLabels` | slider | Show min/max labels |
| `optionQuery(source)` | listSuggester, inlineListSuggester | Query options from vault |
| `class(name)` | all | Add CSS class for styling |
| `defaultValue(val)` | all | Default value if property is empty |
| `showcase` | all | Display mode for examples |

### Bind Target (Property Path)

The part after the `:` specifies which frontmatter property to bind to:

```markdown
`INPUT[toggle:done]`                    Bind to root property "done"
`INPUT[text:metadata.subtitle]`        Bind to nested property
`INPUT[text:metadata["sub key"]]`      Nested with special characters
`INPUT[number:^rating]`                Bind to property in another note (via block ref)
```

**Nested properties** use dot notation: `metadata.rating` binds to:
```yaml
metadata:
  rating: 5
```

### Binding to Other Notes

To bind an input to a property in a different note:

```markdown
`INPUT[toggle:[[Other Note]]#done]`
```

This creates a two-way binding to the `done` property in `Other Note.md`.

---

## VIEW Fields

VIEW fields display frontmatter property values inline — read-only, no editing.

### Syntax

```markdown
`VIEW[{property_name}]`
`VIEW[{property_name}][text]`
`VIEW[{property_name}][link]`
```

### View Types

| Type | Description | Example |
|---|---|---|
| `text` | Plain text display | `VIEW[{status}][text]` |
| `link` | Render as clickable link | `VIEW[{related}][link]` |
| `image` | Render as image | `VIEW[{cover}][image]` |
| `math` | Render as math expression | `VIEW[{formula}][math]` |

### Expressions in VIEW

VIEW fields support JavaScript-like expressions:

```markdown
`VIEW[{rating} * 10]`
`VIEW[{firstName} + " " + {lastName}]`
`VIEW[{done} ? "Complete" : "Pending"]`
```

---

## BUTTON Fields

BUTTON fields create clickable buttons that perform actions.

### Inline Syntax

```markdown
`BUTTON[button_id]`
```

Buttons are defined either inline or via templates in the plugin settings.

### Button Definition (code block)

````markdown
```meta-bind-button
label: Click Me
icon: check
style: primary
actions:
  - type: updateMetadata
    bindTarget: done
    value: true
  - type: open
    link: "[[Other Note]]"
```
````

### Action Types

| Action type | Description |
|---|---|
| `updateMetadata` | Update a frontmatter property |
| `open` | Open a note or URL |
| `command` | Run an Obsidian command |
| `createNote` | Create a new note |
| `replaceInNote` | Replace text in a note |
| `replaceSelf` | Replace the button with content |
| `regexpReplaceInNote` | Regex replace in note |
| `insertIntoNote` | Insert text at a position |
| `inlineJS` | Run JavaScript (requires JS enabled) |
| `sleep` | Wait before next action |
| `templaterCreateNote` | Create note using Templater template |

### Button Styles

`primary`, `default`, `destructive`, `plain`

---

## JavaScript Support

Meta Bind can optionally execute JavaScript in VIEW fields and BUTTON actions.
This is controlled by the `enableJs` setting — **when disabled (the default),
JavaScript expressions in VIEW fields and `inlineJS` button actions will not
execute.**

Check the vault's Meta Bind settings before writing JS-dependent elements.

---

## Configuration Awareness

Key Meta Bind settings that affect agent behavior:

| Setting | Default | Impact |
|---|---|---|
| `enableJs` | `false` | When false, JS in VIEW and BUTTON won't run |
| `preferredDateFormat` | `YYYY-MM-DD` | Date format used by date inputs |
| `excludedFolders` | `[]` | Folders where Meta Bind is disabled |
| `syncInterval` | `200` | How quickly changes propagate (ms) |

---

## Interaction with Other Plugins

### Meta Bind ↔ Properties (Core Plugin)

Both manage frontmatter, but through different interfaces:
- Properties core plugin provides the sidebar Properties pane
- Meta Bind provides inline interactive elements
- Both write to the same YAML frontmatter
- Changes from either side are reflected in the other

**Conflict risk:** If an agent manually edits a frontmatter property that has
a Meta Bind INPUT bound to it, both the agent edit and the Meta Bind sync
are writing to the same field. The last write wins. In practice, this is safe
as long as the agent is not editing while the user is actively interacting
with Meta Bind inputs.

### Meta Bind ↔ Dataview

Meta Bind INPUT fields modify frontmatter values that Dataview queries read.
This creates a live data pipeline: user changes a toggle → frontmatter updates
→ Dataview query refreshes → table/list updates. When writing notes that use
both, ensure property types are consistent with Dataview expectations (see
`references/dataview.md`).

### Meta Bind ↔ Templater

Templater templates can include Meta Bind syntax. When Templater processes
the template on file creation, Meta Bind syntax passes through as-is (it's
inline code, which Templater doesn't process). The resulting note then has
working Meta Bind fields.

### Meta Bind ↔ Kanban

Meta Bind INPUT/VIEW fields placed inside Kanban board files may not render
correctly — the Kanban plugin renders its own card UI, not standard markdown.
Avoid embedding Meta Bind syntax in Kanban boards.

---

## Safe Editing Rules

When programmatically editing notes that contain Meta Bind elements:

### DO:
- Preserve Meta Bind inline code blocks exactly (the `INPUT[...]`, `VIEW[...]`,
  `BUTTON[...]` syntax)
- Edit frontmatter properties that Meta Bind reads — the inputs will reflect
  the change on next sync
- Add new Meta Bind elements to notes

### DO NOT:
- Partially modify Meta Bind syntax (e.g., changing `INPUT[toggle:done]` to
  `INPUT[toggle:complete]` without understanding the property implications)
- Delete frontmatter properties that active Meta Bind inputs are bound to —
  the inputs will show errors
- Write JavaScript in Meta Bind elements without confirming `enableJs` is true

---

## Common Pitfalls

1. **JS disabled by default** — `VIEW[{rating} * 2]` works (it's a simple
   expression), but `VIEW[{items}.length]` requires JS enabled. Check settings.
2. **Property doesn't exist** — An INPUT bound to a nonexistent property
   creates it on first interaction. A VIEW bound to a nonexistent property
   shows nothing (no error).
3. **Type mismatch** — A `toggle` INPUT expects a boolean property. If the
   property is currently a string `"true"`, the toggle may not sync correctly.
   Ensure frontmatter types match the expected input type.
4. **Excluded folders** — Meta Bind won't process syntax in excluded folders
   (configured in settings). If inputs render as plain code, check this.
5. **Nested property paths** — `INPUT[text:metadata.key]` requires the
   `metadata` object to exist in frontmatter. If it doesn't, the first
   interaction creates it.
6. **Bracket escaping** — If your option text contains parentheses or commas,
   you may need to escape them or use the code block syntax instead.
