# Canvas Reference

Canvas provides an infinite whiteboard for spatial organization of notes,
text, images, links, and groups. Canvas files are **JSON** (`.canvas`), not
markdown.

Read this before creating or editing `.canvas` files. Canvas JSON has strict
structural requirements — node IDs, coordinate math, and group containment
rules — that differ fundamentally from markdown editing.

**Official docs:** https://help.obsidian.md/plugins/canvas
**JSON Canvas spec:** https://jsoncanvas.org/

---

## Canvas File Format

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

---

## Node Types

| Type | Key fields | Description |
|---|---|---|
| `text` | `text` | Free text content (supports markdown) |
| `file` | `file` | Embedded note (vault-relative path) |
| `link` | `url` | Embedded web link |
| `group` | `label` | Visual grouping container |

---

## Edge Properties

| Field | Values | Required |
|---|---|---|
| `id` | Unique string | Yes |
| `fromNode` | Node ID | Yes |
| `fromSide` | `top`, `right`, `bottom`, `left` | Yes |
| `toNode` | Node ID | Yes |
| `toSide` | `top`, `right`, `bottom`, `left` | Yes |
| `color` | Color string | No |
| `label` | Edge label text | No |

---

## Node Color and Style

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

---

## Node Sizing and Spacing

Obsidian renders canvas nodes with internal padding and markdown formatting
that makes nodes visually taller than the JSON-specified `height`. Nodes that
are mathematically non-overlapping can visually overlap once rendered.

**Safe minimums:**

| Content type | Minimum height |
|---|---|
| Short text (1 line, e.g. a wikilink) | `80` |
| Multi-line text (3–5 lines) | `120–160` |
| Heading + body (e.g. `### Title` + description) | `180–260` |

**`###` headings add ~40px hidden rendered padding** per heading level
present in the node content. When estimating rendered height, add ~40px
per heading.

**Gaps between nodes:** 40px minimum (20px causes visual overlap), but
**100px recommended** for complex canvases with rich content cards.

**Positioning formula:** `next_node_y = previous_node_y +
previous_node_height + gap` (gap ≥ 40, ideally 100).

---

## File Nodes vs Text Nodes with Links

`type: "file"` nodes always render the file's content as a scrollable
preview — there's no way to make them show as compact "filename only"
cards. At small heights, the content is truncated and looks broken.

**Workaround:** Use `type: "text"` nodes with wikilinks instead:
```json
{
  "id": "my-link",
  "type": "text",
  "text": "📋 [[path/to/file|Display Name]]",
  "x": 0, "y": 0,
  "width": 260, "height": 80
}
```

The wikilink renders as a clickable internal link. Clicking it opens the
target note and highlights it in the file explorer sidebar.

---

## Group Sizing

Groups contain nodes spatially — a node is "inside" a group if its
coordinates overlap with the group's bounding box. Groups need extra
padding beyond the nodes they contain:

| Side | Padding | Reason |
|---|---|---|
| Top | 40px | Room for the group label |
| Bottom | 40px | Visual breathing room |
| Left/Right | 20px each | Side margins |

**Formula:** `group.height = (last_node_y + last_node_height) -
first_node_y + 80`

---

## Edge Labels

Edge labels support **`\n` line breaks** — a literal `\n` in the `"label"`
string renders as a line break in the canvas:

```json
{
  "id": "e1", "fromNode": "a", "fromSide": "right",
  "toNode": "b", "toSide": "left",
  "label": "TGV 6115 · 2h42m\nMay 25", "color": "4"
}
```

**Edge label occlusion:** Edge labels render in the space between connected
nodes. If groups or nodes are too close horizontally, label text gets
partially hidden behind adjacent cards. For left-to-right flows with labeled
edges, maintain **200–300px horizontal gaps** between group boundaries.
Tighter gaps (200px) work for short labels; wider gaps for 2-line labels.

**Layout formula:** `next_group_x = previous_group_x +
previous_group_width + 300`

---

## Rules

- Every node and edge needs a **unique `id`** — use random strings, not
  sequential integers
- Edge `fromNode` and `toNode` must reference valid node IDs
- `file` nodes use vault-relative paths: `"file": "Notes/My Note.md"`
- `link` nodes use URLs: `"url": "https://example.com"`
- Groups contain other nodes spatially (by overlapping coordinates), not by
  reference
- Do not attempt to read or write `.canvas` files as plain text — always
  parse/generate as JSON

---

## Enhanced Canvas (Community Plugin)

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
