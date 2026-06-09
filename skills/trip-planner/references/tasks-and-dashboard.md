# Tasks and Dashboard

How to set up and manage the TASKS.md file and the visual dashboard for trip planning projects.

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

When a task is done, it must be **moved to the Done section** — not just checked off. The full lifecycle:

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

The user already has a task/kanban tool they prefer. Skip `dashboard.html` entirely. Ask the user if TASKS.md needs any special formatting for their tool (e.g., specific frontmatter, section naming, or metadata syntax). If so, format it accordingly; otherwise use plain markdown sections from the template above.

Store the choice in CLAUDE.md under Preferences (e.g., `Task management: Obsidian Kanban`, `Task management: Linear`, `Task management: plain markdown`).

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
