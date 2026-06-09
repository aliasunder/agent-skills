---
name: obsidian-vault
description: >
  Create, edit, and audit notes in an Obsidian vault. Handles frontmatter
  properties, wikilinks, markdown links, embeds, callouts, tasks, block
  references, tags, and Mermaid diagrams. Plugin-aware: Dataview queries
  and inline fields, Tasks plugin emoji syntax, Kanban board editing,
  Meta Bind INPUT/VIEW/BUTTON fields, Templater templates, Bases property
  schemas, and Canvas JSON.

  ALWAYS-ON RULE: If the working directory is inside an Obsidian vault
  (detected by a .obsidian/ directory in the working directory or any
  ancestor, or a CLAUDE.md that identifies the project as vault-embedded),
  this skill should be active for ALL .md file operations — including
  CLAUDE.md, TASKS.md, session logs, and operational docs. The user does
  not need to mention Obsidian; the vault context is implicit.

  Trigger phrases: "create a note", "edit this note", "update frontmatter",
  "add a property", "add tags", "fix the links", "write a Dataview query",
  "fix this callout", "add a task", "edit my Kanban board", "create a
  daily note", "make a template", "update my dashboard", "organize my
  notes", or any note-editing task in a vault context.

  NOT for: .md files outside an Obsidian vault (READMEs in code repos,
  GitHub issue bodies, static-site content like Hugo or Jekyll, project
  documentation in non-vault repos), generic markdown linting unrelated
  to Obsidian rendering, or Obsidian plugin development (that is a
  separate TypeScript skill).
---

# Obsidian Vault Skill

You are working inside an Obsidian vault. Your job is to create and edit notes
that render correctly, stay plugin-compatible, and follow the vault's
established conventions consistently across sessions.

## How This Skill Works

This skill contains the core conventions and safe editing rules needed for
every note interaction. For detailed syntax and plugin-specific rules, it
points to reference files in `references/` that you load when needed — not
upfront.

**Reference files** (read on demand — always read BEFORE acting, not after):

### Obsidian core syntax
- `references/syntax.md` — Full Obsidian Flavored Markdown syntax: block
  references, embeds, Mermaid diagrams, inline comments, footnotes, LaTeX
  math, advanced link patterns. **Read before working with any of these
  constructs.**
- `references/properties.md` — Full YAML property type reference: reserved
  keys, type rules, edge cases, common schemas. **Read before creating or
  repairing frontmatter, or when designing a property schema.**

### Core plugins
- `references/core-plugins.md` — Core Obsidian plugin conventions: Properties
  (type inference), Daily Notes, Templates, Graph, Backlinks, Slides, Sync.
  **Read before working with any core plugin feature — especially property
  type consistency or Daily Notes templates.**
- `references/bases.md` — Bases database views: `.base` file YAML format,
  filter functions (and their gotchas), formulas, property type requirements,
  Bases vs Dataview comparison. **Read before creating or editing `.base`
  files or writing notes that feed into a Bases view.**
- `references/canvas.md` — Canvas JSON format: node types, sizing/spacing
  rules, edge labels, group containment, Enhanced Canvas plugin. **Read
  before creating or editing `.canvas` files.**

### Community plugins
- `references/dataview.md` — Dataview queries, inline fields, DataviewJS,
  implicit fields, cross-plugin interactions. **Read before writing any
  Dataview query or inline field.**
- `references/tasks.md` — Tasks plugin emoji format, custom statuses, dates,
  priority, recurrence, dependencies, query blocks. **Read before writing or
  editing tasks with emoji metadata.**
- `references/kanban.md` — Kanban board markdown structure, card syntax, lane
  management, settings block, safe editing rules. **Read before editing any
  Kanban board file.**
- `references/meta-bind.md` — Meta Bind INPUT/VIEW/BUTTON syntax, property
  binding, action types, JavaScript support. **Read before writing Meta Bind
  interactive elements.**
- `references/templater.md` — Templater syntax, common functions, folder
  templates, JavaScript in templates, core Templates vs Templater. **Read
  before creating or editing template files.**

### Vault access tools
- `references/vault-tools.md` — Three tool tiers for interacting with vaults
  (direct file ops, Obsidian CLI, MCP tools), environment detection, and a
  decision matrix for which tool to use when. **Read when choosing between
  tools for vault operations, or when working in a new environment.**

These files contain the detailed rules that prevent silent rendering errors
and plugin-incompatible output. Reading them after you've already written
something means you'll need to redo it.

---

## Guiding Principles

1. **Render safety first.** Never write Markdown that Obsidian core or
   installed plugins will misparse or silently corrupt.
2. **Minimum viable change.** When editing an existing note, change only what
   was asked. Do not reorder sections, alter heading hierarchy, or modify
   frontmatter keys unless explicitly requested.
3. **Match the vault's link convention.** Do not assume wikilinks or markdown
   links. Detect the convention from existing notes and `.obsidian/app.json`
   (`useMarkdownLinks` setting). If the convention is unclear, ask the user.
   Once established, be consistent — never mix conventions within a vault.
   See "Convention Detection" below for the full process.
4. **Keep metadata stable.** Preserve existing frontmatter keys and their
   types. Add new keys only at the end of the frontmatter block. Check for
   vault-wide type consistency before adding a property name that might
   already exist elsewhere.
5. **Ask before restructuring.** If a request implies renaming notes,
   splitting files, or changing folder structure, confirm before acting —
   these operations can break links vault-wide.
6. **One convention per vault.** If the vault uses Tasks plugin emoji syntax,
   don't introduce plain checkboxes. If it uses Dataview inline fields, don't
   add YAML-only properties. Read existing notes before writing new ones.
7. **Detect, confirm, and persist conventions.** When you notice a convention
   in the vault — link style, frontmatter schema, task format, tag placement,
   date format — check whether it's intentional and persist the finding.
   See "Convention Detection" below.
8. **Look things up — don't guess and iterate.** When you encounter
   unfamiliar Obsidian syntax, a plugin feature you're unsure about, or a
   convention that could go multiple ways, **use WebSearch to check official
   docs and community forums before writing anything.** Each reference file
   links to its plugin's official documentation — start there.

   **Trigger:** If you've tried something and it didn't work, or you're
   about to write syntax you haven't seen confirmed in the vault or in
   these reference files, that's the signal to search — not to try another
   guess. Two failed attempts without searching is too many.

   **What to search:** The Obsidian docs site (`help.obsidian.md`), the
   plugin's own docs (linked at the top of each reference file), the
   Obsidian forum (`forum.obsidian.md`), Reddit (`r/ObsidianMD`), and
   GitHub issues for the relevant plugin. Community plugins especially
   have undocumented behaviors and version-specific quirks that only
   surface in forum threads, Reddit posts, and GitHub issues.

   **Why this matters:** Plugin settings change default behavior, and vaults
   diverge from defaults frequently. Guessing leads to circular
   trial-and-error that wastes time and produces incorrect output. A single
   WebSearch call is faster than three wrong attempts.

---

## Convention Detection and Persistence

Obsidian vaults are highly customizable. Two power users' vaults can look
completely different — different link styles, frontmatter schemas, task
formats, plugin configurations, and organizational patterns. An agent that
assumes defaults will break things.

### On First Use in a New Vault

When this skill activates in a vault for the first time (no prior convention
record exists), detect and confirm these conventions before writing anything:

**1. Link convention — understand the approach, not just the syntax:**
- Check `.obsidian/app.json` for two settings:
  - `useMarkdownLinks` (true = markdown links, false/absent = wikilinks)
  - `newLinkFormat` (`"shortest"` = bare name, `"relative"` = relative path,
    `"absolute"` = full path from vault root). Default is `"shortest"`.
- Read 3-5 existing notes to confirm actual usage matches the settings. Look
  for patterns beyond syntax: Do links use aliases consistently? Are paths
  included even when `shortest` is the setting? Is there a display-name
  convention (e.g., `[[folder/Note|Note]]` for cleaner reading view)?
- Check frontmatter for link properties (`related:`, `parent:`, etc.) — are
  wikilinks quoted? Are they using aliases?
- Look for bidirectional `related:` linking — when note A lists note B in
  `related:`, does note B reciprocate? If this is a convention, persist it
  and follow it on all new links.
- If mixed styles exist, ask the user which convention to follow going
  forward and whether to migrate existing links
- Persist the full picture: link type, path format, alias convention,
  bidirectional linking expectation

**2. Frontmatter schema:**
- Read 3-5 representative notes to identify common property names and types
- Note which properties are used vault-wide vs folder-specific
- Identify any property schemas tied to Dataview queries, Bases views, or
  Meta Bind inputs
- Persist the schema pattern

**3. Task format:**
- Check `.obsidian/plugins/obsidian-tasks-plugin/data.json` for `taskFormat`
  (e.g., `tasksPluginEmoji`)
- Check for custom statuses in the same config
- Check if auto-set dates are enabled (`setCreatedDate`, `setDoneDate`,
  `setCancelledDate`)
- Look at existing tasks in the vault to confirm format in use
- Persist relevant settings

**4. Tag convention — understand the user's taxonomy priorities:**
- Are tags primarily in frontmatter, inline (`#tag`), or both?
- Nested tags (`#project/active`) or flat (`#project`, `#active`)? Is there
  a hierarchy that reflects the user's mental model (e.g., `#project/active`
  vs `#status/active` suggest different organizational philosophies)?
- Casing: lowercase-hyphenated, camelCase, or mixed?
- How heavily does the user rely on tags vs folders for organization? Some
  vaults use folders as the primary axis and tags sparingly for cross-cutting
  concerns; others use tags as the primary taxonomy with a flat folder
  structure. Understanding this shapes how you tag new notes.
- **Numeric-only tags are invalid** — Obsidian rejects purely numeric tags
  (e.g., `2025`) even when YAML-quoted. If you find numeric-ish conventions,
  check how the vault handles them (likely a separate property like `year:`).
- Persist the pattern and the rationale behind it

**5. Date format:**
- What date format appears in frontmatter? (Usually ISO 8601, but check)
- Daily note filename format (from `.obsidian/daily-notes.json`)
- Persist if non-standard

**6. Template usage:**
- Is Templater installed? What's the templates folder?
- Are folder templates configured?
- Is `trigger_on_file_creation` enabled?
- Persist if relevant to file creation

**7. Kanban usage:**
- Are there Kanban board files? What lanes/structure do they use?
- Are board cards using Tasks plugin emoji syntax?
- Is the board used for workflow state (lane position = status) instead of
  custom task statuses?
- Persist the pattern

**8. Daily notes — detect the interaction model:**
- Is the Daily Notes core plugin enabled? Check `.obsidian/core-plugins.json`.
- Where do daily notes live? Check the Daily Notes settings for folder and
  date format.
- Sample 2-3 existing daily notes to understand the structure: Is there a
  template? What sections exist? Are sections auto-managed by a plugin?
- **Check for plugin-managed sections.** Community plugins like
  `obsidian-list-modified` automatically maintain sections in daily notes
  (e.g., "Files Created", "Files Modified"). If such a plugin is active,
  agents must not write to those sections — or to the daily note at all,
  depending on the plugin's scope.
- If daily notes are NOT plugin-managed, ask the user how they'd like agents
  to interact with them: append to a specific section? Leave them alone?
  Update a specific template section?
- If daily notes ARE plugin-managed but the user wants template improvements,
  discuss what sections or content they'd like added or refined in the
  template.
- Persist the interaction model: which sections are off-limits, what agents
  can add, and the template path if relevant.

**9. Hub and index notes — detect navigational patterns:**
- Some vaults use hub notes, Maps of Content (MOCs), or index files
  (e.g., `INDEX.md`, `MOC.md`, or a note with a distinctive name) as entry
  points for folders or topic areas. These aggregate links, embed sections,
  or provide Dataview tables that serve as dashboards.
- Scan a few folders for recurring patterns: Is there always an `INDEX.md`?
  A note that matches the folder name? A note with a `type: moc` property?
- If hub notes exist, they need maintenance: when creating, renaming, or
  deleting a note in a folder with a hub note, check whether the hub needs
  updating (add/remove/rename an entry). This is a vault-specific convention
  — detect it, don't assume it.
- Persist the pattern: which folders have hub notes, what format they use,
  and what maintenance they require.

### What to Look For in Existing Notes

When reading existing notes to detect conventions, watch for:

- **Deviations from defaults** — These are usually intentional. If the vault
  uses markdown links instead of wikilinks, that's a deliberate choice.
  Document it.
- **Inconsistencies** — If some notes use wikilinks and others use markdown
  links, the vault may be in transition. Ask the user which convention to
  follow going forward.
- **Plugin-specific patterns** — Dataview inline fields, Tasks emoji syntax,
  Meta Bind inputs — these indicate which plugins are actively used and how.
- **Folder-specific schemas** — Different folders may have different
  frontmatter schemas. A `projects/` folder might use `status`, `due`,
  `priority` while a `people/` folder uses `role`, `org`, `email`.

### How to Persist Conventions

Once a convention is detected and confirmed by the user, persist it so
future sessions don't need to re-detect:

**Option 1: Project CLAUDE.md (recommended for vault-wide conventions)**

Add a skill invocation callout and a conventions section to the project's
CLAUDE.md. The callout should appear near the top — before any content
sections — so the skill is triggered every session:

```markdown
> **This project uses the obsidian-vault skill.** Invoke
> `obsidian-vault:obsidian-vault` at the start of every session. It contains
> Obsidian editing conventions, plugin reference docs, and safe output rules
> that this project depends on.

## Obsidian Vault Conventions

- **Links:** Markdown links with relative paths (`[text](path.md)`)
- **Tags:** Lowercase hyphenated in frontmatter (`tags: [my-tag]`)
- **Tasks:** Tasks plugin emoji format, auto-set created/done dates
- **Kanban:** Lane position indicates status (no custom checkbox statuses)
- **Date format:** ISO 8601 (`YYYY-MM-DD`)
- **Templates folder:** `Templates/`
```

The callout is a reliability measure — CLAUDE.md is always read at session
start, so embedding the invocation instruction ensures the skill activates
even if the agent wouldn't otherwise reach for it.

**Option 2: Auto-memory (supplemental)**

If auto-memory is available (Cowork and Claude Code), use it for conventions
that are better expressed as behavioral guidance than as a settings list:

```markdown
---
name: obsidian-vault-link-convention
description: User chose markdown links over wikilinks in their Obsidian vault
type: feedback
---

Use markdown links with relative paths in Obsidian vault, not wikilinks.
**Why:** User chose markdown links so agent can read paths for file navigation.
**How to apply:** All .md file creation/editing in the vault uses
`[display](relative/path.md)` format.
```

**When to use which:**
- **CLAUDE.md** — the primary place for vault conventions. It's read at
  session start, it's explicit, and any agent working in this project sees
  it. Put link style, frontmatter schemas, task format, template folder here.
- **Auto-memory** — supplements CLAUDE.md with behavioral context: *why* a
  convention was chosen, correction history ("user asked me to stop doing X"),
  or nuance that doesn't fit a settings list.

### Proactive Convention Surfacing

Don't wait for conventions to cause problems. When editing a vault for the
first time or encountering an unfamiliar pattern:

1. **Notice** — "I see this vault uses markdown links instead of wikilinks"
2. **Confirm** — "Is this intentional? Should I continue this convention?"
3. **Persist** — Store the confirmed convention in CLAUDE.md or auto-memory
4. **Follow** — All subsequent edits respect the convention

If you're about to write something that deviates from a detected convention,
stop and ask. The convention may exist for reasons that aren't obvious from
the syntax alone.

---

## Making Notes Useful in the Vault

Syntax correctness is the floor. A well-integrated note is also *discoverable*
— it shows up in the right Dataview queries, appears in the graph, surfaces
in backlinks, and has metadata that Bases and the Properties pane can work
with. When creating or editing notes, think about how the note will be found
and used *within Obsidian*, not just whether the markdown is valid.

### Properties — when and what to add

Not every note needs the same properties. The guiding question is: **will
this note be queried, filtered, or displayed in a structured view?**

- **Match sibling notes.** Before writing frontmatter, read 1-2 existing notes
  in the same folder. If they share a schema (`title`, `status`, `due`,
  `tags`), the new note should match it — otherwise it's invisible in any
  Dataview table, Bases view, or Tasks query scoped to that folder.
- **Feed the queries that exist.** If a dashboard or Dataview query filters
  by `status` or `tags`, every note in the source folder needs those
  properties to participate. A note missing `status` doesn't show as
  "no status" — it's just absent from results.
- **Commonly valuable properties:**
  - `title` — useful for display in queries and Properties pane
  - `date` or `created` — when the note was created (enables chronological
    sorting and calendar views)
  - `tags` — cross-cutting categorization beyond folder structure
  - `status` — for notes that move through a workflow (active, done, etc.)
  - `aliases` — alternative names for link autocomplete
- **Don't over-property.** Only add properties that serve a query, view, or
  navigational purpose. A property nobody queries is noise in the frontmatter.
- **When in doubt, ask.** If you're creating a note in an unfamiliar folder
  and the schema isn't obvious, ask the user what properties it should have
  rather than guessing.

### Tags — when to add them

Tags make notes findable via the Tag pane, Dataview `FROM #tag` queries, and
search. They're most useful for cross-cutting concerns that don't map to
folder structure.

- **Check the vault's tag taxonomy first.** Browse existing tags (via Tag
  pane or by reading notes) before inventing new ones. Using `#proj` when
  the vault already uses `#project` fragments the taxonomy.
- **Tags vs folders.** A note lives in one folder but can have many tags.
  Use tags for dimensions that cut across folders: topic, status, source,
  context.
- **Nested tags for hierarchy.** `#project/active` and `#project/archived`
  group under `#project` in the Tag pane and in Dataview queries (`FROM
  #project` matches both).
- **Frontmatter vs inline.** Follow the vault's convention — some vaults
  keep all tags in frontmatter, others use inline `#tags` in the body, some
  use both. Don't introduce a new convention.
- **Don't over-tag.** Tags should reflect meaningful categories, not every
  concept mentioned in the note. A note about a meeting doesn't need
  `#meeting` if it already lives in a `meetings/` folder.

### Links — when to create them

Links are the nervous system of a vault. They create graph connections,
populate the Backlinks pane, and make notes navigable. A note with no
outgoing links is an island.

- **Link to existing notes when referencing them.** If you mention a person,
  project, concept, or document that has its own note, link to it. This
  builds the graph and makes both notes more discoverable.
- **Links create bidirectional value.** When note A links to note B, note A
  appears in B's Backlinks pane — this is how users discover connections
  they didn't explicitly navigate to.
- **Don't over-link.** Link when the connection is meaningful and the reader
  might want to navigate there. Linking every mention of a common term
  creates noise. Link the first or most contextually relevant mention.
- **Link to notes that don't exist yet (carefully).** Obsidian supports
  links to nonexistent notes — they show as unresolved in the graph and
  can be created later by clicking. This is useful for forward references,
  but ask before creating dangling links in an unfamiliar vault.
- **Aliases for cleaner prose.** If the note name is awkward in a sentence,
  use display text: `[[Meeting Notes 2025-01-15|last week's meeting]]` or
  `[last week's meeting](Meeting Notes 2025-01-15.md)`.
- **Bidirectional `related:` frontmatter links.** If the vault uses a
  `related:` property in frontmatter to connect notes, maintain
  bidirectionality: when adding note B to note A's `related:` list, add
  note A to note B's `related:` in the same edit pass. This keeps the
  Properties pane, graph view, and Dataview queries symmetrical. Check
  during convention detection whether the vault follows this pattern — if
  it does, every `related:` addition is a two-file operation.

### Embeds — when to use them

Embeds (`![[Note]]` or `![[Note#Section]]`) pull content from other notes
inline. They're powerful for dashboards and summaries but have trade-offs.

- **Use heading or block embeds for specific sections** — `![[Note#Section]]`
  or `![[Note#^block-id]]` — rather than embedding entire notes, which can
  create unwieldy rendering.
- **Dashboards and hub notes** are the primary use case — a central note
  that embeds key sections from multiple source notes for an at-a-glance
  view.
- **Embeds are read-only views.** Editing the embedded content requires
  navigating to the source note. Don't embed content that the user will
  want to edit in place.
- **Embed format follows link convention.** If the vault uses wikilinks,
  embeds are `![[Note]]`. If markdown links, embeds are still `![[Note]]`
  — Obsidian's embed syntax always uses the `![[]]` format regardless of
  the link setting.

---

## Core Obsidian Flavored Markdown

### Frontmatter / Properties

Always placed at the very top of the file, fenced with `---`, before any
content.

```yaml
---
title: Note Title
date: 2025-01-15
tags:
  - project
  - active
aliases:
  - Alternative Name
status: in-progress
---
```

**Core property rules:**
- `tags` and `aliases` — always YAML lists, never inline strings
- Dates — ISO 8601: `2025-01-15` or `2025-01-15T14:30:00`
- Booleans — lowercase `true` / `false`
- Numbers — unquoted: `rating: 4.5`
- Links in properties — always quoted: `related: "[[Other Note]]"`
- No wikilinks inside `tags` or `aliases` values

For the full property type reference and reserved keys, read
`references/properties.md` before creating or repairing frontmatter.

### Frontmatter for Queryable Notes

When creating notes that will be queried by Dataview, Bases, or Tasks,
frontmatter design is critical. Follow these rules:

1. **Check existing notes in the same folder** for the property schema before
   writing new frontmatter. Match names, types, and value formats exactly.
2. **Use consistent types vault-wide** — if `status` is text in one note, it
   must be text everywhere. See `references/core-plugins.md` → Properties.
3. **Prefer explicit typing** — `rating: 4.5` (number) not `rating: "4.5"`
   (string). `done: true` (boolean) not `done: "true"` (string).
4. **Lists for multi-value fields** — `tags: [a, b]` not `tags: "a, b"`.
5. **ISO dates for date fields** — `due: 2025-02-01` not `due: Feb 1`.
6. **Quote wikilinks** — `related: "[[Note]]"` not `related: [[Note]]`.

---

### Internal Links

The vault's link convention determines the format. Detect it before writing.

**Wikilinks (Obsidian default):**
```markdown
[[Note Name]]                    Basic link
[[Note Name|Display Text]]       Link with alias
[[Note Name#Heading]]            Link to heading
[[#Heading in same note]]        Same-note heading link
```

**Markdown links (if `useMarkdownLinks` is true in app.json):**
```markdown
[Display Text](relative/path/to/Note.md)
[Display Text](Note.md#heading)
[Display Text](#heading-in-same-note)
```

Rules for either convention:
- Be consistent — never mix conventions in the same vault
- Obsidian resolves both for graph view, backlinks, and autocomplete
- Wikilinks don't need `.md` extensions; markdown links do
- Both create backlinks and graph connections

For block references, embeds, and advanced link patterns, read
`references/syntax.md`.

---

### Callouts

```markdown
> [!note]
> Default callout with no title.

> [!tip] Custom Title
> Callout with a title.

> [!warning]- Collapsed by default
> Hidden until expanded.

> [!info]+ Expanded by default
> Visible but collapsible.
```

**Supported types:** `note`, `info`, `tip` / `hint` / `important`,
`abstract` / `summary` / `tldr`, `todo`, `success` / `check` / `done`,
`question` / `help` / `faq`, `warning` / `caution` / `attention`,
`failure` / `fail` / `missing`, `danger` / `error`, `bug`, `example`,
`quote` / `cite`

Use callouts for flagged content. Do not substitute callouts for headings.

---

### Tasks

Plain Obsidian tasks:
```markdown
- [ ] Incomplete task
- [x] Completed task
```

If the Tasks plugin is active, use its extended syntax. Read
`references/tasks.md` before writing Tasks plugin syntax. Key decision
points:

- What task format does the vault use? (emoji, Dataview, other)
- Are custom statuses configured? (`[/]`, `[-]`, etc.)
- Are auto-set dates enabled? (created, done, cancelled)
- Does the vault use Kanban lane position instead of custom statuses?

---

### Tags

```markdown
#tag
#nested/tag
#tag-with-hyphens
```

- In frontmatter: always under `tags:` as a list, no `#` prefix
- Nested with `/` separator: `#project/active`
- No spaces; use hyphens or underscores
- **No purely numeric tags** — Obsidian rejects tags like `2025` even when
  YAML-quoted. Use a separate frontmatter property (e.g., `year: "2025"`)
- **Escape `#` when it doesn't mean a tag** — In prose, `#` followed by
  letters or numbers looks like a tag to Obsidian (e.g., PR `#5`, issue
  `#fix-login`). Escape with a backslash (`\#5` renders as #5) or use
  inline code (`` `#5` ``). Common in changelogs, commit references, and
  PR descriptions written inside the vault.
- Check the vault's convention: frontmatter-only, inline-only, or both

---

## Safe Output Rules

Before returning any `.md` file you wrote — whether new or edited — verify:

- [ ] New note opens with a fenced frontmatter block (match the vault's
      existing note schema — read a nearby note first if the schema isn't
      obvious)
- [ ] Frontmatter is at the very top, properly fenced with `---`
- [ ] Frontmatter property types consistent with other notes in the vault
      (no silent string ↔ number ↔ list drift — this breaks Dataview and
      Bases queries)
- [ ] No new frontmatter keys added unless requested (when editing)
- [ ] No existing frontmatter keys removed or renamed (when editing)
- [ ] Links use the vault's established convention (wikilinks OR markdown
      links — not mixed)
- [ ] Existing links are intact — not converted to a different format
      (when editing)
- [ ] Block IDs (`^id`) are preserved if present (when editing)
- [ ] Callout syntax is valid (`> [!type]`)
- [ ] Task syntax is consistent with the vault's convention
- [ ] Tags use correct format (no spaces, nested with `/`)
- [ ] No unescaped `#` in non-tag contexts (PR numbers, issue refs, heading
      mentions in prose — use `\#` or inline code)
- [ ] Inline comments (`%% ... %%`) preserved if present (when editing)
- [ ] No accidental section duplication (when editing)
- [ ] Kanban board structure preserved if editing a board file (settings
      block, blank lines between lanes, lane headings)

---

## Note Audit

When asked to review a note for convention issues:

1. Check frontmatter: valid YAML, correct types, no wikilinks in
   tags/aliases, fenced correctly
2. Check links: using the vault's established convention consistently
3. Check callout syntax
4. Check task syntax consistency
5. Check tag format
6. Check for any markdown that will render incorrectly in Obsidian
7. Check plugin compatibility (Dataview fields, Tasks emoji syntax,
   Meta Bind inputs, Kanban structure)

Report each issue with its location and the corrected version. Do not edit
the file — only report unless told to fix.

---

## Setting Up a New Vault-Embedded Project

If a Cowork or Claude Code project has been created in (or alongside) a
vault and doesn't yet have an Obsidian-aware CLAUDE.md:

1. **Check for `.obsidian/` in the working directory or any ancestor
   directory** — a vault often contains many Cowork projects as
   subdirectories, so `.obsidian/` may live one or more levels above the
   Cowork project root

2. **Run convention detection** — follow the "Convention Detection" process
   above. Read existing notes, check plugin configs, identify patterns.

3. **Recommend a project CLAUDE.md section** — suggest the user add detected
   conventions to their project's CLAUDE.md (or Cowork project settings).
   Example:

   ```
   ## Obsidian Vault
   This project lives inside an Obsidian vault (`parent/directory/`).

   Use the `obsidian-vault` skill for all .md file creation and editing in
   this project — notes, session logs, CLAUDE.md/TASKS.md, and any
   operational docs (they all render in Obsidian since the project lives
   in the vault). Preserve existing links, frontmatter, and note structure
   unless asked otherwise. Ask before renaming notes, moving files, or
   making vault-wide structural changes.

   **Conventions:**
   - **Links:** [detected convention]
   - **Tags:** [detected convention]
   - **Tasks:** [detected convention]
   - **Frontmatter:** [detected schema patterns]
   ```

4. **Note which plugins are active** — check `.obsidian/plugins/` and
   `.obsidian/community-plugins.json` for installed community plugins, and
   `.obsidian/core-plugins.json` for enabled core plugins. Flag which
   reference files will be relevant.

5. **Persist conventions** — store detected conventions in CLAUDE.md and/or
   auto-memory so they survive across sessions.

---

## What This Skill Does Not Do

- Rename or move files without link-safe tooling — if Obsidian CLI is
  available, `obsidian move` handles renames with automatic link updates.
  If not, either ask the user to rename via Obsidian's UI, or rename
  directly and offer to find and update broken links via search. See
  `references/vault-tools.md` for the full tool decision matrix.
- Treat `.canvas` files as plain text — use the Canvas JSON rules in
  `references/core-plugins.md`
- Add Templater syntax to non-template notes
- Reformat an entire vault or batch-rename notes without explicit
  confirmation
- Change existing frontmatter property types without confirmation
- Assume a link convention without checking the vault's actual usage
- Write plugin-specific syntax without first reading the relevant reference
  file
