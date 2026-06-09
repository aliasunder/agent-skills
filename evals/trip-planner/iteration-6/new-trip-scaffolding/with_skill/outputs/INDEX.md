# Project Index

Complete file listing and quick-reference guide for User + Alex's Japan trip (October 2026).

## How to Use This Project

1. **Start here:** Read `CLAUDE.md` first every session (agent working memory)
2. **Check tasks:** Open `TASKS.md` (what's active, waiting, next, done)
3. **Deep dive:** Use file map in `CLAUDE.md` to find specific reference docs
4. **Track progress:** Session logs in `memory/sessions/` document each session's work

## Root Files (Core Working Memory)

| File | Purpose | Read When |
|------|---------|-----------|
| **CLAUDE.md** | Agent working memory, file map, trip status | Every session start |
| **TASKS.md** | Task tracker (Active, Waiting On, Up Next, Done) | Every session start |
| **PROJECT_STRUCTURE.md** | Project overview and structure guide | Understanding project layout |
| **EXECUTION_SUMMARY.txt** | Initial scaffolding details and assumptions | First session reference |
| **SCAFFOLDING_COMPLETE.md** | Phase 1 completion validation | Verifying Phase 1 is done |
| **INDEX.md** | This file — quick reference guide | Navigating the project |

## Memory Files — Core Reference

### Travelers (memory/people/)
| File | Purpose | Read When |
|------|---------|-----------|
| `memory/people/user.md` | User profile (vegetarian, food/temple, booking philosophy) | Activity/comfort/material decisions |
| `memory/people/alex.md` | Alex profile (omnivore, knee constraint, first-Asia, language anxiety) | Activity/comfort/anxiety decisions |

### Projects (memory/projects/)
| File | Purpose | Read When |
|------|---------|-----------|
| `memory/projects/japan-trip-oct2026.md` | Trip overview, budget, decisions, booking status, constraints | Budget/booking/structure questions |

### Operational Files (memory/)
| File | Purpose | Read When |
|------|---------|-----------|
| `memory/itinerary.md` | Day-by-day schedule (skeleton, populated Phases 2-6) | Planning schedule, budget tracking |
| `memory/glossary.md` | Trip shorthand and terminology | Unfamiliar abbreviations |

### Session Logs (memory/sessions/)
| File | Purpose | Read When |
|------|---------|-----------|
| `memory/sessions/session-log-apr02-initial.md` | Initial scaffolding session (Phase 1) | Handoff to Session 2 |

## Memory Files — Created During Phases (Populated as Needed)

### Research (memory/research/)
| Naming Pattern | Purpose | Created During |
|----------------|---------|-----------------|
| `memory/research/{topic}-{city}.md` | Raw research, comparisons, options | Phases 3-6 (flights, hotels, activities, restaurants) |

### Guides (memory/guides/)
| Naming Pattern | Purpose | Created During |
|----------------|---------|-----------------|
| `memory/guides/{city}-activities.md` | Curated activity guide (after user approval) | Phase 5 |
| `memory/guides/{city}-restaurants.md` | Curated restaurant guide (after user approval) | Phase 6 |

### Plans (memory/plans/)
| Naming Pattern | Purpose | Created During |
|----------------|---------|-----------------|
| `memory/plans/{deliverable}.md` | Implementation plan before building complex outputs | Phase 7 (before PDF generation, packing lists, etc.) |

### Reference (memory/reference/)
| Naming Pattern | Purpose | Created During |
|----------------|---------|-----------------|
| `memory/reference/external-systems.md` | Email labels, booking site notes, tool setup | Ongoing (as tools are set up) |

## Output Files (Traveler-Facing Artifacts)

### Primary Outputs (outputs/)
| File | Purpose | Created During |
|------|---------|-----------------|
| `outputs/{deliverable}.pdf` | Traveler-facing PDFs (itinerary guides, decision docs, daily cards) | Phase 7 (Pre-Trip Prep) |

### Tickets & Receipts
| Directory | Purpose |
|-----------|---------|
| `outputs/tickets/` | Downloaded e-tickets, vouchers, confirmations |
| `outputs/receipts/` | Purchase receipts (flights, hotels, activities) |

## Archive (archive/)

| Purpose |
|---------|
| Superseded routing options, rejected alternatives, cancelled bookings (with explanations) |
| Prevents revisiting dead ends |

## Phase Roadmap

**Phase 1 (Define) — ✓ COMPLETE**
- Files: CLAUDE.md, TASKS.md, memory/people/, memory/projects/, memory/glossary.md
- Next: Confirm dates and booking philosophy → proceed to Phase 2

**Phase 2 (Route) — NEXT**
- Read: CLAUDE.md, memory/people/, memory/projects/
- Create: memory/research/route-options.md (comparison), archive/ (rejected options)
- Output: Decision-support doc (PDF) for user + Alex discussion
- Next: Commit to route → proceed to Phase 3

**Phase 3 (Transport)**
- Read: memory/projects/, memory/itinerary.md
- Create: memory/research/flights-*.md, memory/research/trains-*.md, memory/research/transfers-*.md
- Research: Use available tools (Kiwi, travel MCPs, browser)
- Next: Book flights, trains, transfers → proceed to Phase 4

**Phase 4 (Accommodation)**
- Read: memory/people/, memory/projects/, memory/itinerary.md
- Create: memory/research/hotels-{city}.md per city
- Research: One city at a time, present shortlist, get approval, book
- Next: All hotels booked → proceed to Phase 5

**Phase 5 (Activities)**
- Read: memory/people/, memory/itinerary.md
- Create: memory/research/activities-{city}.md, memory/guides/{city}-activities.md
- Plan: One main activity/day, rest breaks, alternatives
- Next: Every day planned → proceed to Phase 6

**Phase 6 (Restaurants)**
- Read: memory/people/, memory/itinerary.md, memory/guides/{city}-activities.md
- Create: memory/research/restaurants-{city}.md, memory/guides/{city}-restaurants.md
- Present: Day-by-day in schedule context
- Next: Every meal has primary + backup → proceed to Phase 7

**Phase 7 (Pre-Trip Prep)**
- Read: references/materials-guide.md (before creating anything)
- Create: memory/plans/{deliverable}.md per output
- Produce: PDF itinerary guide, daily cards, packing lists, language cheat sheet
- Output: Save all traveler-facing materials to outputs/
- Next: All materials complete → proceed to Phase 8

**Phase 8 (Final Check)**
- Read: memory/itinerary.md, memory/projects/
- Verify: All bookings confirmed, budget reconciled, nothing dangling
- Update: TASKS.md (move everything to Done), CLAUDE.md (update status)
- Output: Clean project, ready for departure

## Quick Reference — What to Do

### Starting a session
1. Read CLAUDE.md (status, file map, open questions)
2. Read TASKS.md (what's active)
3. Read the last session log (handoff notes)

### Planning activities
1. Read: memory/people/user.md and memory/people/alex.md
2. Reference: memory/itinerary.md (schedule and constraints)

### Making budget decisions
1. Read: memory/projects/japan-trip-oct2026.md (budget section)
2. Reference: memory/itinerary.md (budget summary)

### Checking preferences
1. Read: CLAUDE.md "Preferences" section
2. Deep dive: memory/people/ (specific traveler details)

### Researching anything
1. Read: references/research-methodology.md (before starting)
2. Create: memory/research/{topic}-{city}.md
3. Present: Findings inline (day-by-day context for activities/restaurants)
4. Approve: Get user sign-off before creating guides

### Booking anything
1. Extract: Confirmation details from email
2. Update: memory/projects/{trip-name}.md (booking details, refs, cancellation policies)
3. Update: memory/itinerary.md (budget status, charged?)

### Creating deliverables
1. Read: references/materials-guide.md (before writing any generation code)
2. Plan: memory/plans/{deliverable}.md (co-review, test workflow)
3. Generate: Code/script per planning docs
4. Test: Open and verify output before presenting to user
5. Save: outputs/ directory

## File Size Summary

- Root files: ~24K (CLAUDE.md, TASKS.md, summaries)
- Memory files: ~22K (profiles, project, itinerary, glossary, session log)
- Total: ~46K (all files created during Phase 1)

## Assumptions Needing Confirmation

All documented in CLAUDE.md "Open Questions":
1. Exact October dates (which weeks?)
2. Booking philosophy satisfaction (confirmed or adjust?)
3. Points strategy (80K for flights acceptable?)
4. Hotel preference (3-4 stars, character-filled confirmed?)

See memory/projects/japan-trip-oct2026.md for full assumption list.

---

**Last Updated:** Apr 2, 2026 (Initial Scaffolding)  
**Phase:** 1 (Define) — Complete  
**Next Phase:** 2 (Route)  
**Project Status:** Ready for Session 2
