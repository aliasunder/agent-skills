# Index — Trip Orchestrator Premature Phase Jump Test

## Quick Navigation

**Start here:** `README.md` — Overview of the test, what was created, how to interpret.

**See the user-facing response:** `response.md` — What the user hears when they ask to research hotels.

**Understand the skill's design:** `SKILL_BEHAVIOR_SUMMARY.md` — Why each decision was made.

**Evaluate the output:** `EVALUATION_NOTES.md` — 8 criteria checked against the skill's behavior.

**See the architecture:** `ARCHITECTURAL_FLOW.md` — How files coordinate across sessions; why scaffolding happens immediately.

## Test Output Files (12 total)

### Documentation / Evaluation
| File | Purpose | Audience |
|------|---------|----------|
| `README.md` | Test overview and navigation | Evaluators |
| `FILE_MANIFEST.txt` | Visual summary of what was created | Evaluators |
| `EVALUATION_NOTES.md` | 8 evaluation criteria with pass/fail | Evaluators |
| `SKILL_BEHAVIOR_SUMMARY.md` | Detailed walkthrough of skill logic | Evaluators |
| `ARCHITECTURAL_FLOW.md` | How files coordinate; why scaffolding happens | Evaluators/Designers |
| `INDEX.md` | This file | Everyone |

### User-Facing Output
| File | Purpose | Audience |
|------|---------|----------|
| `response.md` | The message user receives when asking to research hotels | User (Tanisha) |

### Project Scaffolding — Core Files
| File | Purpose | Session 1 | Session 2+ |
|------|---------|----------|-----------|
| `CLAUDE.md` | Project index; lists Open Questions blocking Phase 1 | Created | Updated (answers recorded) |
| `TASKS.md` | Task tracker; shows Phase 1 active, Phases 2-7 up next | Created | Updated (Phase 1 done, Phase 2 active) |

### Project Scaffolding — Memory Files
| File | Purpose | Session 1 | Session 2+ |
|------|---------|----------|-----------|
| `memory_people_you.md` | Your profile (travel style, dietary, logistics) | Template (TBD) | Filled from Phase 1 answers |
| `memory_people_sam.md` | Sam's profile (same structure) | Template (TBD) | Filled from Phase 1 answers |
| `memory_projects_japan_trip.md` | Trip overview, decisions, bookings, budget | Skeleton | Grows as phases complete |
| `memory_itinerary.md` | Day-by-day schedule | Skeleton (empty) | Filled in Phase 2 (route) |

## How to Read These Files

**If you want to understand the test:**
1. Read `README.md` (overview)
2. Read `FILE_MANIFEST.txt` (visual summary)
3. Check `EVALUATION_NOTES.md` (criteria)

**If you want to understand the skill's logic:**
1. Read `SKILL_BEHAVIOR_SUMMARY.md` (what it did and why)
2. Read `ARCHITECTURAL_FLOW.md` (how files work together)

**If you want to understand what the user experiences:**
1. Read `response.md` (the message they get)
2. Skim `CLAUDE.md` (what the project looks like to them)
3. Skim `TASKS.md` (what they need to do next)

**If you want to implement this in your own trip-planner:**
1. Read `ARCHITECTURAL_FLOW.md` (core pattern)
2. Study `CLAUDE.md` and `TASKS.md` (file format)
3. Review `EVALUATION_NOTES.md` (how to check your implementation)

## The Test in One Sentence

**When a user tries to skip ahead in trip planning phases, the skill detects it, explains why it's inefficient, lists the blocking questions, creates full project scaffolding, and redirects them constructively — not by refusal, but by education.**

## Key Files Explained

### response.md (64 lines)
The user sees this when they ask to research Kyoto hotels. It:
- Acknowledges their excitement
- Explains why Phase 1 comes first (not arbitrary gatekeeping)
- Lists 8 specific Phase 1 questions
- Promises efficient hotel research once Phase 1 is done
- Frames it as "saving you from doing research twice"

### CLAUDE.md (48 lines)
Project index. At session start, user checks this to see:
- Current phase (Phase 1: Define)
- Open Questions (8 items blocking progress)
- File map (where everything is)
- Trip snapshot (dates, cities, budget — all TBD)

### TASKS.md (58 lines)
Task tracker. At session start, user checks this to see:
- Active: Phase 1 (with 9 specific subtasks)
- Up Next: Phases 2-7 (waiting for Phase 1 to complete)
- Waiting On: Phase 1 answers
- Done: (empty, no work done yet)

### ARCHITECTURAL_FLOW.md (200+ lines)
Deep dive into:
- Why scaffolding happens immediately (not after Phase 1)
- How files coordinate across sessions
- The decision tree for which files to create
- Why TASKS.md is the workflow engine
- Information flow from Phase 1 through Phase 7

## Test Success Criteria

The skill passes if:
1. ✅ User receives warm explanation (not refusal)
2. ✅ Phase violation is detected
3. ✅ Blocking questions are specific and actionable
4. ✅ CLAUDE.md created with Open Questions
5. ✅ TASKS.md created with Phase 1 active
6. ✅ Memory files scaffolded (ready to be filled)
7. ✅ Files link together properly
8. ✅ Next session, user can read CLAUDE.md and TASKS.md to know exactly where they are

## What This Proves

A trip-planning skill that:
- Enforces phase dependencies (not as hard rules, but as efficiency)
- Uses TASKS.md and CLAUDE.md as coordination mechanisms
- Scaffolds full project structure immediately (even if early phases incomplete)
- Redirects gracefully with education, not gatekeeping
- Maintains cross-session continuity via on-disk files

This is a testable, repeatable pattern for multi-session, multi-phase planning workflows.
