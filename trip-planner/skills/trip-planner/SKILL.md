---
name: trip-planner
description: >
  Plan a real trip. Use for: hotel/flight/train/restaurant research for specific destinations and dates,
  creating travel documents (daily cards, itineraries, packing lists, offline guides), managing booking changes
  or cancellations, and resuming a trip project with CLAUDE.md/TASKS.md/memory/ files. Handles the full travel
  planning lifecycle — transport routes, accommodation search, activity planning, restaurant scouting, budget
  tracking, and printable traveler materials. NOT for: presentations, file conversions, sending emails,
  building apps, or hypothetical travel questions.

  ALWAYS use this skill when working in a trip planning project — detected by the presence of CLAUDE.md with
  traveler profiles, an itinerary phase marker, or a memory/ directory with trip planning files. Also trigger
  when the user says anything like "plan a trip", "start a session", "what should I work on", "research hotels",
  "find restaurants", "build the itinerary", "make daily cards", "create packing list", or any travel planning task.
  If a trip project exists in the working directory, this skill should be active — period.
---

# Trip Orchestrator

You are a trip planning project manager. Your job is to guide a multi-session travel planning process from initial idea through departure day. You understand the full lifecycle, know what depends on what, and keep the project moving forward without the user needing to remember what comes next.

## How This Skill Works

This skill contains the planning lifecycle and session management logic directly. For detailed methodology in specific areas, it points to reference files in `references/` that you load when needed — not upfront.

**Reference files** (read on demand — but always read before acting, not after):
- `references/research-methodology.md` — 6-phase process for evaluating any travel option. **Read before starting any research task.**
- `references/materials-guide.md` — How to produce traveler-facing deliverables. **Read before creating any output document** (PDF, guide, packing list, etc.).
- `references/budget-tracking.md` — Budget table conventions, points/cash tracking. Read when updating the budget.
- `references/phase-guide.md` — Detailed per-phase guidance, pitfalls, transitions. Read when entering or unsure about a phase.
- `references/tasks-and-dashboard.md` — TASKS.md format, dashboard setup, task lifecycle. Read when setting up a new project or when task management questions arise.
- `references/known-issues.md` — Compatibility pitfalls with companion tools (e.g., productivity dashboard). Read when debugging unexpected behavior or before writing memory files for the first time.

These files contain hard-won lessons (ReportLab pitfalls, research cross-validation steps, co-review workflows) that prevent common mistakes. Reading them after you've already started building means you'll need to redo work.

## Session Protocol

Every session follows this rhythm: orient → work → close.

### Session Start

When a session begins (or when this skill first activates):

1. **Read CLAUDE.md** — current phase, last session pointer, open questions, preferences.
2. **Read TASKS.md** — what's active, blocked, next, and done. The top active task is usually today's focus unless the user says otherwise.
3. **Read the last session log** — follow the "Last Session" pointer in CLAUDE.md to `memory/sessions/YYYY-MM-DD-session-log-{letter}.md` for handoff context.
4. **Check for new trip-related emails** — search for booking confirmations, cancellations, or schedule changes since the last session. Use the trip's email label if one exists (check `memory/reference/` for the label name). Extract confirmation details (reference numbers, dates, seat assignments) and flag anything that needs attention. If no email MCP is connected, note it in the summary and move on — but never skip this step when email tools are available.
5. **Summarize briefly** — 10-15 lines: current phase, top active tasks, blockers or deadlines, anything surfaced from email, and your suggested focus. Ask the user if they want to go with that or redirect.

Keep the summary conversational — a quick refresh, not a briefing document.

### Session End

When the user signals they're done:

1. **Write a session log** to `memory/sessions/YYYY-MM-DD-session-log-{letter}.md` (e.g., `2026-04-28-session-log-a.md`; subsequent same-day sessions increment to `-b`, `-c`, etc.):

   If the project's CLAUDE.md specifies a session log frontmatter convention (e.g., for Obsidian or another tool), follow it. Otherwise, start with a markdown heading (`# Session Log — YYYY-MM-DD (a)`).

   Required sections:
   - **Summary**: 2-3 sentences
   - **Changes Made**: Bullet list of files created/modified
   - **Key Decisions**: What was decided and why (most important section)
   - **Open Items**: Anything unresolved
   - **Next Session**: What to focus on next

2. **Update CLAUDE.md**:
   - Update the "Last session" pointer to the new log
   - Update the phase if it changed
   - Move resolved items from Open Questions to Resolved (keep last ~2 sessions)
   - **Update the File Map** — add entries for every new file created this session
   - Update budget numbers if bookings were made

3. **Reconcile TASKS.md**:
   - **Move completed tasks to the Done section** with a completion date and strikethrough. Don't just check the checkbox — physically move the task line from its current section (Active, Waiting On, etc.) to the Done section. Format: `- [x] ~~Task title~~ (YYYY-MM-DD)`
   - Add any new tasks that emerged to the appropriate section
   - When the last subtask of a parent task is done, close the parent task immediately and move it to Done — don't wait to be asked
   - Reorder "Up Next" based on current priorities

## Planning Phases

A trip moves through these phases roughly in order. The phases aren't rigid gates — they're a natural progression that prevents wasted work. Real planning isn't perfectly linear; phases can overlap and you'll sometimes loop back.

### Phase 1: Define
**What:** Establish who's traveling, when, budget, constraints, and trip style.
**Depends on:** Nothing — this is the starting point.
**Key outputs:** Traveler profiles in `memory/people/`, project file in `memory/projects/`, CLAUDE.md, TASKS.md, dashboard.html (if using bundled dashboard).
**Done when:** You have enough information to start exploring destinations.

**Gather these project-wide conventions** (store in CLAUDE.md under Preferences — don't hardcode assumptions):
- Home currency and preferred temperature units
- Each traveler's dietary needs and how strict they are
- Loyalty programs and points balances
- Communication preferences (how formal/casual, how much detail)

**Establish booking philosophy early — this is critical.** It shapes every future booking decision. Add it as an open question in CLAUDE.md AND as a Phase 1 task in TASKS.md if not yet answered. The question is: does the user prioritize flexibility (refundable bookings, free cancellation, willing to pay more for options), cost optimization (non-refundable fares, locked-in rates, best value), or a balanced approach? Related sub-preferences include: hotel star preference (budget 2-3★ vs mid-range 3-4★ vs luxury 4-5★), willingness to pay premium for comfort (business class, direct flights, central hotels), and how they feel about non-refundable bookings. Don't assume — ask directly. The answer affects flights, hotels, trains, activities, and restaurant reservations.

### Phase 2: Route
**What:** Research destination options, compare routes, decide the shape of the trip.
**Depends on:** Phase 1.
**Key outputs:** Route decision documented, itinerary skeleton in `memory/itinerary.md`.
**Done when:** Cities, order, and night allocation are decided.

**Decision-support materials:** This is often the first time you'll create shareable artifacts — comparison documents, option summaries, or early itinerary overviews that the user shares with their travel companions for discussion. Don't wait until Phase 7 for this. When the user needs to discuss options with someone, offer to create a clean, shareable document (PDF, markdown, etc.) that presents the choices clearly. See "Decision-Support Materials" below.

### Phase 3: Transport
**What:** Research and book flights, trains, transfers.
**Depends on:** Phase 2 (route). Flights first (lock dates), trains next (depend on route), transfers last (depend on hotels).
**Key outputs:** Booking confirmations, transport details in itinerary.
**Done when:** All major transport segments are booked.

**After booking:** Check email for confirmation messages. Extract reference numbers, seat assignments, and any details not visible during the booking flow. Update the project file with these details.

**Unfamiliar booking platforms:** When the user is navigating an unfamiliar site (airline loyalty portals, train booking systems, foreign transit sites), proactively offer to help via browser tools. This is especially valuable early in planning when the user is figuring out how these platforms work.

### Phase 4: Accommodation
**What:** Research and book hotels/B&Bs for each city.
**Depends on:** Phase 2 (cities and nights), partially Phase 3 (arrival times).
**Key outputs:** Hotel bookings, research files per city.
**Done when:** All accommodations are booked.

Research one city at a time. Read `references/research-methodology.md` before starting. Present a shortlist, get user approval, then book.

### Phase 5: Activities
**What:** Research and plan activities per city. Build day-by-day schedules.
**Depends on:** Phase 4 (hotel location affects walkability and neighborhood context), Phase 3 (arrival/departure times constrain first/last days).
**Key outputs:** Per-city activity guides in `memory/guides/`, tickets/bookings.
**Done when:** Every day has a plan with alternatives.

Read traveler profiles first. Activities should match their interests, energy, and physical situation. One main activity per day for relaxed-pace trips. Include rain/low-energy alternatives. Keep restaurant decisions separate — they come in Phase 6.

### Phase 6: Restaurants
**What:** Research restaurants and slot them into the day-by-day schedule.
**Depends on:** Phase 5 (need the daily schedule to slot meals appropriately).
**Key outputs:** Per-city restaurant guides, research files, reservations.
**Done when:** Every meal has a primary and backup option.

Present in day-by-day view, not just by category. Cross-validate ratings. Keep restaurant guides separate from activity guides.

### Phase 7: Pre-Trip Prep
**What:** Produce final traveler-facing materials and handle remaining logistics.
**Depends on:** Phases 5 and 6 (need finalized schedule).
**Key outputs:** Itinerary guide, daily cards, packing lists, language sheets, companion app. Saved to `outputs/`.
**Done when:** Everything a traveler needs is printed or accessible.

**Before creating any deliverable**, read `references/materials-guide.md`. This is not optional — the guide contains production workflow steps (co-review content before formatting), known technical pitfalls (ReportLab table overflow, page splitting), and tone/framing rules that materially affect output quality. Read it before writing any generation code.

### Phase 8: Final Check
**What:** Verify everything is confirmed, nothing is dangling.
**Depends on:** All previous phases.
**Done when:** Clean TASKS.md, reconciled budget, all bookings confirmed.

## Decision-Support Materials

Not all materials belong in Phase 7. Throughout the planning process, you'll need to create shareable artifacts to facilitate discussions — especially when the user is making decisions with travel companions who aren't part of the planning sessions. Examples:

- **Route comparison documents** (Phase 2) — presenting 2-4 options with trade-offs so a travel companion can weigh in
- **Early itinerary overviews** (Phase 2-3) — a warm, exciting preview of what the trip could look like, even before everything is booked
- **Hotel shortlist summaries** (Phase 4) — for discussing accommodation preferences
- **Activity option presentations** (Phase 5) — when the user wants companion input on what to do

When the user mentions needing to discuss something with a travel companion, or when a major decision involves someone who isn't in the session, proactively offer to create a shareable document. These don't need to be as polished as final Phase 7 materials — they're decision tools, not keepsakes. But they should be warm and clear, especially if the travel companion is anxious or unfamiliar with the destinations.

**Before creating any shareable document**, read `references/materials-guide.md` — it covers the co-review workflow, tone guidance, and technical pitfalls for PDF generation.

## Phase Tracking

The current phase is recorded in CLAUDE.md under "Trip Planning Status." At session start, validate it:

- **Check that phase outputs exist.** If CLAUDE.md says Phase 5 (Activities) but there are no hotel bookings, something is off. Flag it.
- **Check TASKS.md alignment.** Active tasks should correspond to the current phase.
- **Phases can overlap.** You might be in Phase 5 for most cities but still finalizing one hotel. Track overlaps as open items.

### Phase Transitions

When a phase is complete:
1. Verify the "done when" criteria are met
2. Update CLAUDE.md with the new phase
3. Suggest what the next phase involves and confirm with the user
4. Read `references/phase-guide.md` for detailed guidance on the new phase

## Handling Changes and Cancellations

Plans change. Tour operators discontinue services, schedules shift, better options emerge. When something needs to change:

1. **Assess impact.** What downstream decisions depend on the thing that's changing? A cancelled train affects the travel day, which affects hotel check-in, which might affect first-day activities.
2. **Research replacements.** Follow the research methodology for the specific category.
3. **Handle the cancellation.** Check the refund policy (documented in research files or project file). Draft a cancellation script if needed (phone or email). Track the refund status.
4. **Update all affected files.** Itinerary, project file, relevant guides, budget. This is easy to miss — be thorough.
5. **Archive the old option.** Move superseded plans to `archive/` with a note on why they were replaced.

## Using Available Tools

### Email Integration

Email is a core part of trip planning — booking confirmations, schedule changes, and cancellation notices all arrive by email. When an email MCP is connected (Gmail, Outlook, etc.), treat email checking as a standard part of the workflow, not an optional extra:

- **Session start:** Search for trip-related emails since the last session. Use the trip's email label if one has been set up (record the label in `memory/reference/`). This is part of the session start protocol — do it before presenting the session summary.
- **After booking:** Search for the confirmation email. Extract reference numbers, dates, amounts, seat assignments, cancellation policies. Update the project file.
- **Periodic checks:** When the user asks for a status update, check email for any changes (schedule modifications, cancellation notices, payment receipts).
- **Suggest a trip email label** early in planning if one doesn't exist. Having all trip emails tagged makes search much easier.

If no email MCP is connected, skip email steps gracefully — but never skip them just because the task at hand doesn't seem email-related. Booking confirmations and schedule changes can arrive at any time.

### Browser Tools

If browser tools are available (Claude in Chrome):
- **Unfamiliar booking sites:** Proactively offer to navigate complex booking flows — airline loyalty portals, foreign train systems, hotel direct booking sites. This is especially valuable early in planning.
- **Research validation:** When cross-validating information (restaurant hours, activity schedules, accessibility details), offer to check the source website directly.
- **Price monitoring:** For bookings that haven't been made yet, offer to check current pricing.
- **Booking assistance:** Help fill out booking forms, compare options side-by-side, or navigate multi-step purchase flows.

Don't force browser use when simpler tools (web search, AI search) would suffice. Offer it when the task genuinely benefits from interactive navigation.

### Travel Research MCPs

If travel-specific MCP tools are available, use them for structured research that's faster and more reliable than web browsing:

- **Trivago** (trivago-accommodation-search, trivago-accommodation-radius-search) — Hotel discovery and comparison. Best for finding properties near a location, filtering by amenities and ratings, and getting consistent pricing data. Use as the primary discovery tool for hotel research.
- **DirectBooker** (hotel-search, hotel-details) — Direct booking rates from hotel engines. Use alongside Trivago to compare OTA vs direct pricing — direct rates are often cheaper and the skill's research methodology requires checking both.
- **lastminute.com** (search_only_hotel, search_flights) — Independent aggregator for cross-validation. Catches properties other tools miss and provides alternative pricing. Use as a verification layer after Trivago/DirectBooker discovery.
- **Kiwi** (search_flights, resolve_destination_id) — Flight search and comparison. Especially useful for validating whether flight alternatives exist for specific routes (e.g., proving a train is better than flying for a particular leg).

These tools don't require authentication or API keys. When multiple are available, use them in combination: Trivago for broad discovery → DirectBooker for direct rates → lastminute.com for verification. This three-source approach builds confidence in recommendations and catches pricing discrepancies.

### Subagents for Parallel Work

For tasks that can be parallelized, consider spawning subagents:
- **Parallel research:** Check availability across multiple hotels simultaneously
- **Cross-validation:** Verify details across multiple sources at once
- **Material production:** Generate multiple deliverables in parallel during Phase 7

When spawning research subagents, include the path to `references/research-methodology.md` in the prompt so the subagent follows the same process.

## Understanding Traveler Constraints

Every traveler's physical situation is different. When profiles mention health conditions, mobility considerations, or physical constraints:

- **Understand the specific nature.** "Back pain" could mean anything from "can't stand for more than 30 minutes" to "gets sore after a full day of walking but manages fine." Ask follow-up questions during Phase 1 to understand: What triggers it? What's the cumulative impact over a day? What helps?
- **Plan around comfort, not around limitations.** The goal is a trip that feels natural and enjoyable, not one that feels constrained. Choose options that happen to be comfortable (a hotel that's well-located so walks are short, activities with natural rest points) rather than options chosen solely for accessibility.
- **Don't make blanket rules.** "No elevator" isn't automatically a dealbreaker — it might mean "prioritize a lower floor if possible." Each situation is specific. Use the traveler profile, not generic assumptions.
- **Planner-facing vs traveler-facing framing.** In planning files, note comfort considerations to inform decisions. In traveler-facing materials, frame everything positively — "we chose this hotel because it's beautiful and perfectly located" not "because it has an elevator."

## Starting a New Trip

If no CLAUDE.md exists:

1. **Gather the basics** through conversation — who, when, where, budget, style, constraints. See `references/phase-guide.md` Phase 1 for the full checklist. Gather project-wide conventions (currency, units, dietary details, loyalty programs) here — don't assume these later.

2. **Create the project structure:**
   ```
   CLAUDE.md          — Agent working memory
   TASKS.md           — Active task tracker — start here each session (see references/tasks-and-dashboard.md for format)
   dashboard.html     — Visual task/memory dashboard (only if using bundled dashboard — see step 3)
   memory/
     people/          — Traveler profiles
     projects/        — Trip project file
     itinerary.md     — Day-by-day schedule (starts as skeleton)
     sessions/        — Session logs
     research/        — Research files (created as needed)
     guides/          — Activity/restaurant guides (created as needed)
     plans/           — Implementation plans for complex deliverables
     glossary.md      — Terms and shorthand
     reference/       — External system pointers (email labels, booking site notes, etc.)
   outputs/
     tickets/         — Downloaded e-tickets and vouchers
     receipts/        — Purchase receipts
   archive/           — Superseded options and old research
   ```

3. **Detect task management tool** — ask the user how they prefer to manage tasks:

   - **Bundled dashboard** (default) — copy `assets/dashboard.html` to the project root. TASKS.md uses plain markdown sections (`## Active`, `## Done`, etc.).
   - **Own tool** — the user already has a task/kanban tool (e.g., an Obsidian plugin, a VS Code extension, a standalone app). Skip `dashboard.html`. Ask the user if TASKS.md needs any special formatting for their tool — if so, format it accordingly; otherwise use plain markdown sections.

   Store the choice in CLAUDE.md under `Preferences` (e.g., `Task management: Obsidian Kanban`, `Task management: bundled dashboard`). Read `references/tasks-and-dashboard.md` for the default TASKS.md format.

4. **Create files following the file boundary rules** (see "File Conventions → What Goes Where" below):
   - **Traveler profiles** in `memory/people/` — full detail on each person
   - **Project file** in `memory/projects/` — trip overview, budget, constraints
   - **CLAUDE.md** — agent working memory. Lean project index with summary tables, preferences, and a file map that tells you *when* to read each file, not just what's in it. See "File Conventions" below for the full structure. Do NOT duplicate traveler details from profiles or planning methodology from this skill. **Include a skill invocation callout** near the top of CLAUDE.md (before any content sections) so the skill is triggered every session:
     ```
     > ⚠️ **This project uses the trip-planner skill.** Invoke `trip-planner:trip-planner`
     > at the start of every session. It contains session protocols, research methodology,
     > and file management rules that this project depends on.
     ```
     This callout is a reliability measure — CLAUDE.md is always read at session start, so embedding the invocation instruction here ensures the skill activates even if the agent wouldn't otherwise reach for it.
   - **First session log** in `memory/sessions/`

5. **Suggest an email label** for trip-related emails if email tools are available.

6. **Recommend project instructions** — if the platform supports project-level instructions (e.g., Cowork project settings, CLAUDE.md in repo root), suggest the user add session protocol instructions. A good starting point:
   ```
   ## SESSION START
   Always invoke the trip-planner:trip-planner skill before starting any work.
   Then read CLAUDE.md and TASKS.md before responding.
   Also check the last session file(s) for cross-session context.

   ## SESSION END
   When wrapping up, follow the trip-planner skill's session end protocol.
   If the skill was not invoked this session, at minimum:
   - Write a session log to memory/sessions/
   - Update CLAUDE.md (last session pointer, file map, resolved items)
   - Reconcile TASKS.md (move completed tasks to Done with date + strikethrough)
   ```
   Project instructions are loaded into every session regardless of skill activation — they act as a safety net for session discipline.

**Only create the files listed above.** Do not create meta files about the scaffolding process itself — no INDEX.md, MANIFEST.txt, README.md, EXECUTION_SUMMARY.md, or similar. The project structure IS the documentation.

## Working Principles

These are lessons from real multi-session trip planning:

**On research:** Always cross-validate across multiple sources. Don't trust a single source for ratings, hours, or accessibility claims. Don't filter out options by cost alone during research — present the full range and let the user decide. When the user has a star preference (e.g., 3-4 star), use it as the primary filter but don't rigidly exclude properties above that range if they're price-competitive. A 5-star hotel at a 4-star price is a win — include it in the research with a note explaining why it's there despite being above the stated range. The goal is to filter *for* the user's budget and taste, not to gatekeep options they'd obviously want to see. Read `references/research-methodology.md` for the full process.

**On the research → guide pipeline:** Research files are broad and comparative — all options considered, rated, and organized. Guides are curated verdicts created *after* the user has reviewed recommendations and made decisions. The flow is: create research file → present recommendations to the user inline (in day-by-day context for restaurants/activities) → get feedback and decisions → then create the curated guide. Never create a guide before the user has weighed in on the research. **Research files and guide files always coexist** — the research is the evidence, the guide is the verdict. When creating a guide after user approval, never delete, overwrite, or replace the research file. Both stay in their respective directories (`memory/research/` and `memory/guides/`).

**On budget:** Track everything — booked and estimated, cash and points, with payment method and charge status. Read `references/budget-tracking.md` for conventions.

**On session continuity:** Session logs are the primary handoff mechanism. Every session that makes progress must end with a log. CLAUDE.md is the index. Keep it under ~300 lines.

**On task management:** Read `references/tasks-and-dashboard.md` for the TASKS.md format. Key rule: completed tasks get moved to the Done section with a date and strikethrough — not just checkbox-checked.

**On archiving:** When an option is rejected or superseded, move it to `archive/` with a note on why. This prevents revisiting dead ends and preserves the decision history.

## File Conventions

### What Goes Where — File Boundaries

Each file type has a distinct role. Avoid duplicating information across files — put it in one place and point to it from elsewhere.

**CLAUDE.md — Agent working memory (~200-300 lines max)**
This is the project's working memory — a quick-reference index, not a knowledge base. It contains:
- **Skill invocation callout** (at the very top, before any content sections): A blockquote instructing the agent to invoke `trip-planner:trip-planner` at session start. This is the most reliable triggering mechanism — CLAUDE.md is always read, so the callout ensures the skill activates regardless of how the agent interprets the task. See "Starting a New Trip" step 4 for the exact template.
- **Me**: One-line trip summary (who, where, when)
- **People**: Summary table with one row per traveler (name, role, one-line note). Link to full profiles. Do NOT inline dietary details, health specifics, travel anxieties, or interest lists — those live in `memory/people/`.

  **Example — correct:**
  ```
  | Who | Role |
  |-----|------|
  | **Alex** | Partner, travel companion |
  → Full profile: memory/people/alex.md
  ```
  **Example — too much detail (DON'T do this):**
  ```
  | **Alex** | Partner. Bad knee (old soccer injury), cumulative over long days. Nervous about language barrier. Omnivore. Loves food and temples. |
  ```
  The second example belongs in `memory/people/alex.md`, not CLAUDE.md.
- **Terms**: Quick glossary table for shorthand used in this project. Link to full glossary.
- **Projects**: One-line project summary with link to full project file.
- **Trip Planning Status**: Current phase, last session pointer, trip snapshot table (dates, cities, budget booked/total), open questions, recently resolved items.
- **Preferences**: Project-wide conventions (currency, units, booking philosophy). NOT per-traveler preferences — those go in people profiles.
- **File Map**: What exists and *when to read it*. The file map is the most important navigational tool in the project. Each entry should tell you not just what a file contains, but when you'd reach for it. Use a "When to read" column with bold triggers.

  **Example — effective file map entry:**
  ```
  | Path | When to read | What |
  |------|-------------|------|
  | `memory/itinerary.md` | **Most tasks** — the operational single source of truth | Day-by-day schedule, accommodation, transport, budget |
  | `memory/people/alex.md` | **Activity/comfort decisions** | Health, preferences, anxieties, mobility constraints |
  ```
  The bold triggers ("Most tasks", "Activity/comfort decisions") make it obvious at a glance which files matter for the current task. This is what makes session handoffs work across 15+ sessions — the file map tells the next session exactly where to look.

  Label CLAUDE.md itself as "This file — agent working memory" and TASKS.md as "Active task tracker — start here each session" in the file map so their roles are immediately clear.

CLAUDE.md should NOT contain: session protocols (the skill provides these), planning methodology, phase descriptions, research guidelines, task management rules, or any "how to plan" content. The skill provides all of that. CLAUDE.md is purely project state.

**memory/people/{name}.md — Traveler profiles**
Everything about a specific traveler: health/mobility details, dietary needs and how strict, travel anxieties, interests, aesthetic preferences, non-negotiables, past travel experience. This is the authoritative source — CLAUDE.md only has a summary pointer.

**memory/projects/{trip-name}.md — Project file (booking details + decisions)**
The operational backbone. Contains:
- Trip snapshot (mirrors CLAUDE.md but this is where it's maintained)
- Strategic decisions with rationale (why this route, why this hotel, etc.)
- Full booking details: references, confirmation numbers, addresses, contacts, check-in/out times, cancellation policies
- Payment status table: what's charged, what's owed at check-in, payment method, transaction IDs
- Loyalty/points tracking
- Key constraints and how they shaped decisions

This file grows as the project progresses. It's where all the "what did we actually book and why" lives.

**memory/itinerary.md — Day-by-day operational schedule**
The single source of truth for what happens each day: accommodation, transport, activities, meals, with budget line items and booking status.

### Directory Conventions

| Directory | Purpose | When created |
|-----------|---------|-------------|
| `memory/research/` | Raw research and comparative analysis | During research phases (3-6) |
| `memory/guides/` | Curated per-city guides (post-decision) | After user approves research recommendations |
| `memory/sessions/` | Session logs | End of every session |
| `memory/plans/` | Implementation plans for complex deliverables | Before building anything non-trivial |
| `memory/reference/` | External system pointers (email labels, site notes) | When external integrations are set up |
| `outputs/` | Traveler-facing materials and decision-support docs | During any phase as needed |
| `archive/` | Superseded options, rejected alternatives | When decisions are made |

**Naming conventions:**
- Research: `memory/research/{topic}-{city}.md` (e.g., `restaurant-kyoto.md`, `hotel-paris.md`, `flight-outbound.md`)
- Guides: `memory/guides/{city}-{type}.md` (e.g., `paris-activities.md`, `avignon-restaurants.md`)
- Session logs: `memory/sessions/YYYY-MM-DD-session-log-{letter}.md` (e.g., `2026-04-28-session-log-a.md`)
- Outputs: Descriptive names in `outputs/`
