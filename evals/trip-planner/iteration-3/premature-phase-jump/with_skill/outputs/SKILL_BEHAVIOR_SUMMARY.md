# Skill Behavior Summary — Handling Premature Phase Jump

## Scenario
User says: "My partner Sam and I are planning a trip to Japan — maybe 10 days in October? We haven't really figured out the details yet but I've been reading about ryokans and I'm super excited. Can you start researching hotels in Kyoto for us?"

This is a **premature phase jump** — the user is asking to jump directly to **Phase 4 (Accommodation)** without completing the foundational work of **Phase 1 (Define)** and **Phase 2 (Route)**.

## What the Skill Did

### 1. **Recognized the Phase Violation**
The skill detected that hotel research (Phase 4) cannot proceed efficiently without:
- Exact dates (affects availability and pricing)
- Full destination list (affects hotel location and transit decisions)
- Budget constraints (affects which ryokans are realistic)
- Booking philosophy (affects whether to prioritize flexibility or cost)
- Traveler profiles (affects accommodation preferences)

### 2. **Redirected to Phase 1**
Instead of starting hotel research immediately, the skill:
- Explained WHY jumping to Phase 4 would waste effort (research done now would change once route is locked)
- Identified all missing Phase 1 information
- Provided a specific list of questions to answer first
- Assured the user this wasn't a delay — it was preventing rework

### 3. **Set Up Project Structure**
Even though Phase 1 isn't complete, the skill created the full directory structure:
- **CLAUDE.md** — project index with "Open Questions" section listing Phase 1 items
- **TASKS.md** — task tracker with Phase 1 tasks clearly marked as "Active" (next up), subsequent phases in "Up Next"
- **memory/people/** — starter traveler profiles for you and Sam (mostly TBD fields)
- **memory/projects/** — trip overview file with status table showing everything is TBD pending Phase 1
- **memory/itinerary.md** — skeleton schedule (empty, to be filled after Phase 2)

### 4. **Framed the Conversation**
The response was:
- **Warm and understanding** — acknowledged the excitement about ryokans
- **Explanatory** — showed specifically why Phase 1 comes first (not arbitrary gatekeeping)
- **Action-oriented** — gave a concrete list of 8 questions to answer
- **Forward-looking** — promised that after Phase 1, hotel research would be "laser-focused and fast"

## Key Skill Principles Demonstrated

1. **Phase Dependencies Are Real** (from SKILL.md §Planning Phases)
   - Phase 2 (Route) depends on Phase 1 (Define)
   - Phase 4 (Accommodation) depends on Phase 2 (Route)
   - Skipping phases creates rework downstream

2. **Validate Phase Alignment** (from SKILL.md §Phase Tracking)
   - "Check that phase outputs exist"
   - If user asks for Phase 4 work but Phase 1 isn't done, flag it

3. **Set Up Scaffold Immediately** (from SKILL.md §Starting a New Trip)
   - Create full directory structure including all phase task lists
   - Use TASKS.md to show where the user currently is and what's blocked
   - Link files together so context flows naturally

4. **Use CLAUDE.md for Open Questions** (from SKILL.md §File Conventions)
   - CLAUDE.md has an "Open Questions" section specifically for Phase-blocking items
   - This is where the user sees at a glance what info is still needed

5. **Session Protocol → Check TASKS.md First** (from SKILL.md §Session Protocol)
   - Next session, the user will open TASKS.md and see "Phase 1: Define" is Active
   - They'll see the specific tasks: "Confirm exact dates", "List all destinations", etc.
   - The task list is now the project's source of truth for "what's blocking us"

## Why This Matters

If the skill had jumped to Phase 4 research:
- ❌ Would research Kyoto ryokans without knowing if Kyoto is 3 nights or 7 nights
- ❌ Would recommend ryokans at price points before budget was set
- ❌ Would do detailed research on cities not yet confirmed
- ❌ Next session: "I love this ryokan but it's not on the route anymore" or "this is outside budget"

By redirecting to Phase 1:
- ✅ Phase 1 answers → Phase 2 route is fast (options are clear)
- ✅ Phase 2 route → Phase 4 hotel research targets the right cities/nights
- ✅ Hotel research is efficient and won't need to be redone

## Files Created

All scaffolding files are provided in `outputs/`:

```
outputs/
  response.md                      # User-facing response (explanation of redirect)
  CLAUDE.md                        # Project index
  TASKS.md                         # Task tracker with Phase 1 active
  memory_projects_japan_trip.md    # Trip project file
  memory_itinerary.md              # Skeleton itinerary
  memory_people_you.md             # Your profile (fields TBD)
  memory_people_sam.md             # Sam's profile (fields TBD)
```

In a real session, these would be organized as:
```
japan-trip/
  CLAUDE.md
  TASKS.md
  memory/
    projects/japan-trip.md
    itinerary.md
    people/you.md
    people/sam.md
    sessions/               # (empty until first session ends)
    research/              # (empty until Phase 3-6)
    guides/                # (empty until decisions made)
  outputs/                 # (empty until deliverables created)
  archive/                 # (empty)
```

## Skill Configuration

This behavior comes from the skill's **Phase Tracking** and **Starting a New Trip** sections, which enforce:
1. Phases have dependencies
2. Phase violations are redirects, not errors
3. Full scaffolding happens immediately even if early phases are incomplete
4. TASKS.md and CLAUDE.md guide users through phases sequentially
