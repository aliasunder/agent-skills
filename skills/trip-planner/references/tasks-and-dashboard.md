# Tasks and Dashboard

How to set up and manage the TASKS.md file and the visual dashboard for trip planning projects.

## Convention Overrides

Every format in this file is a **default**, not a mandate. If the project's CLAUDE.md specifies a convention — typically in a `Conventions` section, often marked "(overrides trip-planner default)" — that convention wins. Check CLAUDE.md **before** creating or reconciling TASKS.md, not after.

One thing is never overridable: **completed tasks physically move to the Done section/lane.** Section or lane position is the source of truth for task status. Only the *syntax* of completion (strikethrough + date, Tasks-plugin emoji dates, or other metadata) varies by convention.

**Example override** — a project living in an Obsidian vault, using the Kanban and Tasks plugins, might have this in its CLAUDE.md:

```markdown
## Conventions

- **TASKS.md** (overrides trip-planner default): Obsidian Kanban board, Tasks plugin
  emoji syntax. Card order: description → ➕ created / ✅ completed dates → block ID (^id).
  Completed cards get a ✅ date and move to the ## Done lane. The Kanban plugin manages
  its own frontmatter and settings block — don't touch them.
```

Under that convention, a completed task looks like this — no strikethrough, no parenthesized date:

```markdown
- [x] Research Paris hotels ➕ 2026-03-10 ✅ 2026-03-15 ^paris-hotels
```

The same rule applies beyond task format: session log frontmatter, date formats, file naming, and link style can all be overridden by CLAUDE.md. When a convention is established there, follow it consistently and don't mix it with the defaults below.

## TASKS.md Format

Use this exact template when creating a new TASKS.md:

```markdown
# Tasks

## Active

## Waiting On

## Someday

## Done
```

You can add additional sections that make sense for the project. Common additions for trip planning:
- **Up Next** — tasks queued for the next session
- **Blocked** — tasks waiting on external input

### Task Format

```markdown
- [ ] **Task title** — context, details, due date if any
  - Sub-bullet for additional context
  - Sub-bullet for links or references
```

### Completing Tasks

When a task is done, it must be **moved to the Done section** — not just checked off. The lifecycle below uses the default completion syntax — if CLAUDE.md Conventions specifies a different one (see "Convention Overrides" above), steps 3–4 use that syntax instead; step 5 — moving to Done — always applies. The same goes for the Parent Tasks examples that follow. The full lifecycle:

1. Find the task in its current section (Active, Waiting On, etc.)
2. Change `[ ]` to `[x]`
3. Add strikethrough: `~~Task title~~`
4. Add completion date in parentheses
5. **Move the entire task** (including sub-bullets) to the Done section

**Before:**
```markdown
## Active
- [ ] **Research Paris hotels** — need elevator or low floor, central location
  - Check Booking.com, hotel direct sites, Google Maps reviews
```

**After:**
```markdown
## Done
- [x] ~~**Research Paris hotels**~~ (2026-03-15)
  - Shortlisted 6, booked Relais du Vieux Paris
```

### Parent Tasks

When the last subtask of a parent task is completed, close the parent task immediately and move it to Done. Don't wait to be asked.

**Before:**
```markdown
## Active
- [ ] **Restaurant research — all cities**
  - [x] ~~Paris~~ (2026-03-26)
  - [x] ~~Avignon~~ (2026-03-26)
  - [x] ~~Verona~~ (2026-03-26)
  - [x] ~~Venice~~ (2026-03-26)
```

**After:**
```markdown
## Done
- [x] ~~**Restaurant research — all cities**~~ (2026-03-26)
  - [x] ~~Paris~~ (2026-03-26)
  - [x] ~~Avignon~~ (2026-03-26)
  - [x] ~~Verona~~ (2026-03-26)
  - [x] ~~Venice~~ (2026-03-26)
```

## Task Management Tool Detection

During project scaffolding, ask the user which tool they prefer for managing tasks. The answer determines whether `dashboard.html` is included and how TASKS.md is formatted.

### Option 1: Bundled Dashboard (default)

The trip-planner plugin bundles a `dashboard.html` — a visual kanban board for TASKS.md with a memory browser. It's a standalone single-file app with no external dependencies.

**Setup:**
1. Copy `dashboard.html` from the plugin's `assets/` directory to the project's root directory
2. The dashboard reads and writes to `TASKS.md` and `memory/` in the same directory
3. It auto-saves changes and watches for external edits
4. Supports drag-and-drop reordering of tasks between sections

Tell the user: "The dashboard is ready at `dashboard.html` — open it from your file browser to see your tasks and memory visually."

TASKS.md uses plain markdown sections (the template above).

### Option 2: Own Tool

The user already has a task/kanban tool they prefer. Skip `dashboard.html` entirely. Then handle the format the way the obsidian-vault skill handles vault conventions — check what's established, confirm, persist, follow:

1. **Detect** — check what's already established: an existing TASKS.md (lanes, card syntax, plugin settings blocks), a CLAUDE.md Conventions section, or tool config files (e.g., the Tasks plugin's `data.json` for its task format).
2. **Confirm** — restate what you found ("Your board uses Tasks-plugin emoji dates and block IDs — should TASKS.md follow that?"). If nothing is established, ask what the tool needs (specific frontmatter, section naming, metadata syntax).
3. **Persist** — record the confirmed format in CLAUDE.md under `Conventions`, marked "(overrides trip-planner default)". Record the tool *choice* under `Preferences` (e.g., `Task management: Obsidian Kanban`, `Task management: Linear`, `Task management: plain markdown`).
4. **Follow** — all subsequent TASKS.md writes use the persisted convention. If you're about to write something that deviates from it, stop and ask — the convention may exist for reasons that aren't obvious from the syntax alone.

If the tool needs nothing special, use the plain markdown sections from the template above.

## Task Conventions for Trip Planning

- **Bold** the task title for scannability
- Include "for [person]" when it's a commitment (e.g., "for Mom")
- Include "due [date]" or "by [date]" for deadlines
- Include "since [date]" for waiting items
- Group related tasks as sub-bullets under a parent task
- Keep Done section for the current planning phase, then trim older completed items (they're preserved in session logs)

## Extracting Tasks from Sessions

During a session, new tasks often surface (follow-up research, bookings to make, confirmations to check). Add them to TASKS.md as they come up — don't wait for session end. Place them in the appropriate section:

- **Active** — can be worked on now
- **Waiting On** — blocked on something external (email reply, booking confirmation, companion's decision)
- **Up Next** — queued for a future session
- **Someday** — nice-to-have, no urgency
