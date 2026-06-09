# START HERE — Trip Orchestrator Premature Phase Jump Test

## What This Test Does

This folder contains the output of a trip-planning skill responding to a user who tries to skip ahead in the planning process.

**User request:** "Can you research hotels in Kyoto for me?"

**User situation:** Brand-new Japan trip (~10 days in October). Excited about ryokans. Has NOT yet:
- Fixed exact dates
- Decided on other destinations (Kyoto only, or Tokyo too?)
- Set a budget
- Decided on booking philosophy (flexibility vs cost)
- Discussed travel style with Sam

**What should happen:** Skill should detect that hotel research (Phase 4) cannot proceed without foundational work (Phase 1: Define). Redirect constructively.

## The Test Output (13 files, 1,100+ lines)

### Core Output for Evaluators
1. **START with `INDEX.md`** — Navigation guide to all files
2. **Then read `README.md`** — Overview of what was created and why
3. **Then read `FILE_MANIFEST.txt`** — Visual summary in plain text
4. **Review `EVALUATION_NOTES.md`** — 8 criteria checked against skill behavior
5. **Deep dive: `ARCHITECTURAL_FLOW.md`** — How files coordinate across sessions

### Files Demonstrating Skill Behavior
- **`response.md`** — What the user hears when they ask to research hotels
- **`SKILL_BEHAVIOR_SUMMARY.md`** — Detailed walkthrough of why each decision was made
- **`CLAUDE.md`** — Project index with Open Questions blocking Phase 1
- **`TASKS.md`** — Task tracker showing Phase 1 as active

### Project Scaffolding (Ready for Next Session)
- **`memory_people_you.md`** — Your profile template
- **`memory_people_sam.md`** — Sam's profile template
- **`memory_projects_japan_trip.md`** — Trip project file
- **`memory_itinerary.md`** — Day-by-day skeleton itinerary

## The Key Insight

When a user tries to skip ahead, a good trip-planning skill:

1. **Detects** the phase violation (hotel research requires route decision)
2. **Explains** why it's inefficient (not gatekeeping, but efficiency)
3. **Lists** what's blocking (8 specific Phase 1 questions)
4. **Scaffolds** the project immediately (CLAUDE.md + TASKS.md + memory files)
5. **Redirects** constructively (with education, not refusal)

This is testable and repeatable across trips.

## How to Evaluate

**Does the skill enforce phase sequencing?**
- Check `response.md` — Is the redirect warm and explanatory?
- Check `TASKS.md` — Does Phase 1 appear as "Active" blocking other phases?
- Check `EVALUATION_NOTES.md` — How many of 8 criteria pass?

**Does the skill maintain cross-session continuity?**
- Check `CLAUDE.md` — Do "Open Questions" guide the user?
- Check `TASKS.md` — Can user open this next session and know where they are?
- Check `ARCHITECTURAL_FLOW.md` — Is the file coordination pattern clear?

**Is the redirect constructive (not gatekeeping)?**
- Read `response.md` — Does it explain the reason or just say "no"?
- Read `SKILL_BEHAVIOR_SUMMARY.md` — Is the logic transparent?

## Quick Facts

| Metric | Value |
|--------|-------|
| Total files created | 13 |
| Total lines of content | 1,100+ |
| Total disk size | 80 KB |
| User-facing files | 1 (response.md) |
| Core project files | 2 (CLAUDE.md, TASKS.md) |
| Memory scaffolding | 4 (people + projects + itinerary) |
| Evaluation docs | 6 (README + INDEX + MANIFEST + BEHAVIOR + FLOW + NOTES) |

## Files by Purpose

### If you want to understand what the user experiences
→ Read `response.md` (what they hear) + `CLAUDE.md` + `TASKS.md` (what the project looks like)

### If you want to understand the skill's logic
→ Read `SKILL_BEHAVIOR_SUMMARY.md` (why each decision) + `ARCHITECTURAL_FLOW.md` (how it all fits)

### If you want to evaluate the output
→ Read `EVALUATION_NOTES.md` (8 criteria) + `README.md` (pass/fail summary)

### If you want to implement this pattern yourself
→ Study `CLAUDE.md` and `TASKS.md` format + Read `ARCHITECTURAL_FLOW.md` + Check `EVALUATION_NOTES.md` for what to test

## The Core Pattern (One Diagram)

```
User: "Research hotels in Kyoto!"
  ↓
Skill: Detects Phase 4 request, but Phase 1 incomplete
  ↓
Skill: Creates CLAUDE.md with Open Questions + TASKS.md with Phase 1 active
  ↓
Skill: Sends response.md explaining redirect + lists 8 blocking questions
  ↓
User sees: Warm explanation + clear next steps + project structure ready
  ↓
User answers Phase 1 questions
  ↓
Next session: Skill reads CLAUDE.md, sees questions answered, marks Phase 1 done
  ↓
Skill moves to Phase 2: Route research
  ↓
After Phase 2: Phase 4 hotel research is now efficient (route locked)
```

## What This Proves

A multi-phase trip planner can:
- ✅ Detect phase violations
- ✅ Redirect gracefully (with education, not refusal)
- ✅ Scaffold full project structure immediately
- ✅ Maintain cross-session continuity via files
- ✅ Use task lists as the coordination engine
- ✅ Prevent wasted research by enforcing dependencies

This is a reusable pattern for any multi-session, multi-phase planning workflow.

---

**Ready to dive deeper?** Open `INDEX.md` for the full navigation guide.
