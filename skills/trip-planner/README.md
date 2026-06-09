# trip-planner

Plan a multi-week trip with Claude that actually remembers everything between sessions. The skill acts as a project manager for your trip — tracking what's been decided, what's next, and producing real deliverables you can print and travel with. Built and refined across 25+ sessions of planning a real two-week Europe trip.

## What you get

**Session memory.** Every session picks up exactly where the last one left off. Claude reads your project state, checks for new booking confirmations in email, and gives you a status summary before asking what to work on.

**Systematic research.** Hotels, restaurants, activities, and transport all go through a 6-phase research process: broad discovery, cross-validation across multiple sources, structured comparison tables, and a shortlist presented for your input before anything gets decided.

**Itinerary at every zoom level.** One living document tracks the entire trip — all days at a glance with cities, transport, and accommodation, then detailed per-day schedules with activities, restaurants, and alternatives. Every booking has its confirmation reference, payment method, and cancellation policy recorded. High-level snapshot for quick orientation, full detail when you need it.

**Budget tracking.** One running ledger across all bookings — cash, points, multi-currency — with payment status, refundability, and a buffer estimate that updates as you go.

**Printable travel materials.** The skill produces real deliverables: day-by-day itinerary guides, printable daily cards (one page per travel day with schedules, restaurants, alternatives, and emergency info), language cheat sheets, and packing lists.

**Phase-based workflow.** Eight planning phases with dependency rules prevent premature decisions — you won't be researching restaurants before cities are chosen, or building packing lists before activities are set.

**Visual task dashboard.** A standalone kanban board that reads your task file, so you can see what's active, waiting, and done at a glance.

## How it works

The skill defines 8 planning phases:

1. **Define** — who's traveling, when, budget, constraints, booking philosophy
2. **Route** — destination research, city selection, night allocation
3. **Transport** — flights, trains, transfers
4. **Accommodation** — hotels/B&Bs per city
5. **Activities** — day-by-day schedules per city
6. **Restaurants** — dining research and reservation planning
7. **Pre-Trip Prep** — itinerary guides, daily cards, packing lists, language sheets
8. **Final Check** — verify everything, reconcile budget

Each session follows a rhythm: **orient** (read project state, last session log, check email, summarize) → **work** → **close** (write session log, update project index, reconcile tasks).

Detailed methodology lives in `references/` files that get loaded on demand — only when the agent enters a research task, creates a deliverable, or updates the budget. This keeps the core skill lean (~320 lines) while having deep guidance available when needed.

## Getting started

Install via npx:

```bash
npx skills add aliasunder/agent-skills --skill trip-planner
```

To start planning: just tell your agent you want to plan a trip. The skill auto-triggers whenever it detects a trip planning project in your working directory, or when you mention anything related to travel planning. The first time, the agent will ask about who's traveling, when, where, and your budget. Everything else flows from there.

## Why a skill?

Multi-week trips can run across 10-20 planning sessions. The skill encodes planning methodology — research cross-validation, budget conventions, session handoffs, deliverable production, phase sequencing — refined over 25+ sessions of planning a real two-week trip. The core skill is ~320 lines; the rest lives in reference files that only load when needed (starting a research task, producing a deliverable, updating the budget).

Your project files stay focused on your trip. The planning process stays in the skill, out of your way. Install, say "plan a trip," and the whole system is there.

The project the skill creates — CLAUDE.md, TASKS.md, memory structure, file conventions — stands on its own. CLAUDE.md acts as **agent working memory** with file maps, traveler profiles, preferences, and session pointers. TASKS.md tracks what's active, blocked, and done. If you open the folder in a future session without the skill, the project context still works. The skill adds the methodology layer on top.

## Design rationale

<details>
<summary>Why a single orchestrator, not multiple skills</summary>

Most skill repos use a **toolkit pattern** — a collection of independent skills where each handles one task (review code, run incident response, research hotels). That works well when skills are genuinely independent.

Trip planning is different. It's a **sequential, phase-dependent workflow** that runs across many sessions: you can't research restaurants before choosing cities, can't build an itinerary before booking hotels, and every session needs to pick up exactly where the last one left off. A multi-skill approach (separate skills for setup, research, itinerary building, session management) breaks down because:

- **Skills can't invoke each other at runtime.** When the agent activates a research skill, it doesn't see the session management skill's rules for how to close a session.
- **The agent has to guess which skill to use.** A user saying "let's pick up where we left off and research restaurants" needs both session management and research, but only one skill loads.
- **Cross-skill consistency relies on file contracts.** Multi-skill plugins coordinate through shared files (CLAUDE.md, TASKS.md, memory/), which works if every skill respects the same conventions — but the agent only sees one skill's instructions at a time.

The orchestrator pattern solves this by putting the full workflow context — phases, dependencies, session protocol, file boundary rules — in a single skill, and deferring detailed methodology to reference files loaded on demand. This gives the same modularity as separate skills without the handoff problem.

**When to use which pattern:**

- **Toolkit (multiple skills):** Independent capabilities that don't depend on each other. Good for: engineering tools (code-review, incident-response), productivity skills.
- **Orchestrator (single skill + references):** Sequential workflows with phase dependencies and multi-session state. Good for: project management, trip planning, any multi-week planning process.

</details>

<details>
<summary>Other key design decisions</summary>

- **File boundary rules.** CLAUDE.md is a lean index (~200-300 lines), not a knowledge base. People details live in `memory/people/`, booking details in `memory/projects/`, methodology in the skill. This prevents the common failure mode where CLAUDE.md becomes a 500-line dump of everything.
- **Research → Guide pipeline.** Research files are broad comparisons. Guides are curated picks created *after* the user approves recommendations. This prevents premature commitment and gives the user a decision point.
- **Booking philosophy gathered early.** Flexibility vs cost, hotel star preference, refundability appetite — gathered in Phase 1 because it shapes every downstream booking decision.
- **Project stands alone.** The CLAUDE.md, TASKS.md, and memory structure the skill creates work independently of the skill. Open the folder in any future session and the project context is there — the skill adds methodology, not lock-in.

</details>

## Structure

```
skills/trip-planner/
  SKILL.md                            # Core skill — phases, session protocol, file conventions
  references/
    research-methodology.md           # 6-phase research process
    materials-guide.md                # How to produce traveler-facing deliverables
    budget-tracking.md                # Budget table conventions
    phase-guide.md                    # Detailed per-phase guidance
    tasks-and-dashboard.md            # TASKS.md format and dashboard setup
    known-issues.md                   # Compatibility pitfalls
  assets/
    dashboard.html                    # Standalone kanban board for TASKS.md
```

## Evals

See the `evals/trip-planner/` directory at the repository root for test scenarios and results.
