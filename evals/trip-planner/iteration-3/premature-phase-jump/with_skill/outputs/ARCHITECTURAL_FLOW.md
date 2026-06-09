# Architectural Flow — How the Skill Handles Phase Violations

## User Request Flow

```
User: "Can you research hotels in Kyoto?"
  │
  ├─→ [SKILL DETECTS]
  │   "Hotel research = Phase 4"
  │   "Current context = brand new project (no CLAUDE.md)"
  │   "Phase 1 (Define) not complete"
  │   RESULT: Phase violation detected
  │
  ├─→ [RESPONSE STRATEGY]
  │   1. Don't refuse or gatekeep
  │   2. Explain why Phase 1 is blocking
  │   3. List specific blocking questions
  │   4. Create project scaffolding anyway
  │   5. End with forward-looking promise
  │
  └─→ [OUTPUT]
      User gets response.md explaining the redirect
      Project structure created (CLAUDE.md, TASKS.md, memory/)
```

## File Creation Decision Tree

```
DECISION: Should each file be created for a brand-new project?

CLAUDE.md
  ├─ YES — Create immediately
  └─ Reason: Project index needed for next session; records Open Questions

TASKS.md
  ├─ YES — Create immediately
  └─ Reason: Shows which phase is active; guides task sequence

memory/people/{name}.md
  ├─ YES — Create templates immediately
  └─ Reason: Gathering Phase 1 info requires profile structure

memory/projects/{trip}.md
  ├─ YES — Create immediately
  └─ Reason: Records decisions and booking status as they happen

memory/itinerary.md
  ├─ YES — Create skeleton immediately
  └─ Reason: Structure needed; content comes in Phase 2

memory/research/
  ├─ NO — Don't create yet
  └─ Reason: Research phase (3-6) hasn't started; premature

memory/guides/
  ├─ NO — Don't create yet
  └─ Reason: Guides only after user decides; premature

memory/sessions/
  ├─ SKIP for now — Will create session log at end of NEXT session
  └─ Reason: This session has no work completed yet (only redirection)

outputs/
  ├─ NO — Main outputs come in Phase 7
  └─ Reason: Deliverables (itinerary guide, daily cards) are end-of-trip

archive/
  ├─ NO — Not needed yet
  └─ Reason: No decisions made; no options to archive
```

## Information Flow Across Sessions

### Session 1 (Current) — Phase 1 Definition
```
Input:  User says "Japan trip, ryokans, Kyoto, ~10 days October"
        Asks to research hotels (premature)

Processing:
  1. Skill detects phase violation
  2. Redirects to Phase 1
  3. Creates CLAUDE.md with Open Questions
  4. Creates TASKS.md with Phase 1 as Active
  5. Creates memory file templates

Output: response.md + scaffolding files
        + List of 8 blocking questions

Result: User sees clear guidance on what's needed
        Project structure ready for next session
```

### Session 2 (Next) — Phase 1 Completion
```
Input:  User answers Phase 1 questions:
        - Dates: Oct 12-22, 2026
        - Destinations: Kyoto, Osaka, Tokyo
        - Budget: $5000 total, $250/night hotels
        - Booking: Flexibility > cost (free cancellation)
        - Sam: Friend, first time Japan, food-focused
        - Dietary: Tanisha vegetarian, Sam omnivore

Processing (Skill at session start):
  1. Reads CLAUDE.md → "Open Questions" section
  2. Sees all Phase 1 info is now provided
  3. Reads TASKS.md → Moves Phase 1 to Done
  4. Reads memory/people/* → Sam's profile complete
  5. Checks CLAUDE.md: Phase 1 done → Move to Phase 2

Output: Updates TASKS.md, moves to Phase 2: Route

Work:   Phase 2 research (destinations, order, nights per city)
        ├─ Create memory/research/destinations-japan.md
        ├─ Compare Tokyo-Osaka-Kyoto routes
        ├─ Decide: Tokyo (3N) → Osaka (2N) → Kyoto (3N)
        └─ Update memory/itinerary.md with skeleton schedule

End-of-session:
  - Write memory/sessions/session-log-apr02-a.md
  - Update CLAUDE.md: "Last session: session-log-apr02-a"
  - Update TASKS.md: Move Phase 2 to Active
```

### Session 3 & Beyond — Phases 3-7
```
Following the same pattern:

Session 3: Phase 3 (Transport)
  Input: Route locked → research flights, trains, transfers
  Output: Bookings confirmed

Session 4: Phase 4 (Accommodation)
  Input: Cities & dates locked → research hotels per city
  Output: Hotels booked (NOW, after Phase 1 & 2 are done)

Session 5: Phase 5 (Activities)
  Input: Hotels locked → research activities per city
  Output: Activity guides created

Session 6: Phase 6 (Restaurants)
  Input: Schedule locked → research restaurants
  Output: Restaurant guides created

Session 7: Phase 7 (Pre-Trip Prep)
  Input: Everything booked → create traveler materials
  Output: PDFs, daily cards, packing lists, language sheets
```

## Why Scaffolding Happens Immediately

**Principle:** Project files are the coordination mechanism across sessions.

If scaffolding didn't happen until Phase 1 was done:
- Session 1: Redirect (no files created)
- User does Phase 1 work offline
- Session 2: "Now let me start... wait, no CLAUDE.md, no TASKS.md"
- Wasted time recreating structure

Instead:
- Session 1: Redirect + full scaffolding
- User sees CLAUDE.md/TASKS.md structure immediately
- Next session: Open CLAUDE.md → see exactly what's blocking
- File structure guides the entire project

## The Key Pattern: Use TASKS.md as the Workflow Engine

```
TASKS.md is not just a list.
It's the project's source of truth for "what phase are we in?"

At any session start:
  1. Open TASKS.md
  2. Look at which section has items: Active, Up Next, Waiting On?
  3. That tells you where you are in the project

Example at Session 1 end:
  Active:    Phase 1: Define (8 tasks, all unchecked)
  Up Next:   Phases 2-7
  Waiting On: Phase 1 answers
  ✓ Message: "We're stuck on Phase 1. Answer those 8 questions and we can move."

Example at Session 2 start:
  User provides Phase 1 answers
  Skill updates TASKS.md:
  Active:    Phase 2: Route (5 tasks)
  Up Next:   Phases 3-7
  Done:      Phase 1: Define ✓ (Apr 2)
  ✓ Message: "Phase 1 complete! Moving to Phase 2: Route research."
```

## File Dependencies & Timing

```
CLAUDE.md
  ├─ Points to TASKS.md (what's active?)
  ├─ Points to memory/people/* (who's traveling?)
  ├─ Points to memory/projects/* (what's the trip?)
  └─ Lists Open Questions (what's blocking current phase?)

TASKS.md
  ├─ Shows current phase (which section is Active?)
  ├─ Guides next work (what's in Up Next?)
  └─ Explains blockers (what's in Waiting On?)

memory/people/
  ├─ Input: Phase 1 questions
  └─ Used by: All phases (activities, restaurants, transport all depend on traveler constraints)

memory/projects/
  ├─ Input: Decisions made (destinations, hotels, restaurants)
  └─ Used by: Booking, payment tracking, constraints reference

memory/itinerary.md
  ├─ Input: Phase 2 (route), Phase 3 (transport times), Phase 4 (hotel times), Phase 5-6 (activities/restaurants)
  └─ Output: Single source of truth for "what happens each day?"

memory/research/
  ├─ Input: Created during Phases 3-6 research
  └─ Used by: Decision making, justifying why we picked certain options

memory/guides/
  ├─ Input: Created after user approves research recommendations
  └─ Output: Traveler-facing guides (separated from research)
```

## Core Insight: Why Phase Sequencing Matters

Without enforced phases:
- User researches 10 Kyoto hotels before deciding on 3 vs 7 nights → 70% wasted effort
- User picks favorite ryokan before knowing budget → Can't afford it, must start over
- User plans activities before booking flights → Arrival time changes, schedule is invalid
- Each session redoes previous work

With enforced phases:
- Phase 1: Lock dates, budget, destinations
- Phase 2: Route based on locked info
- Phase 3: Transport based on locked route
- Phase 4: Hotels based on locked cities/nights
- Each phase builds on previous; no rework

The skill's redirect is not obstruction. It's **efficiency**.

---

## Decision: Why No Session Log Yet?

In a real session, a log is written at session END if work was done.

Here:
- Session 1 = user requested work
- Skill redirected
- No work was actually done
- User hasn't provided Phase 1 answers yet

So: No session log created.

When user DOES answer Phase 1 questions (Session 2):
- That's when work happens
- That's when session log gets written
- CLAUDE.md updated to point to new log

This keeps session logs as a record of "work done," not "time spent redirecting."
