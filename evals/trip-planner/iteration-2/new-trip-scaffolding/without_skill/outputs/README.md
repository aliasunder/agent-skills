# Japan Trip Planning Project — Structure & Workflow

**Trip:** 10 days in Japan, October 2026 (dates TBD)
**Travelers:** You & Alex
**Budget:** $8,000 USD total
**Frequent Flyer:** 85,000 United miles

---

## Project Status (Session 0 — Scaffold)

This is the **initial scaffold** for a multi-session trip planning project. The directory structure, core files, and memory system are now in place. Research and booking will happen in Sessions 1–3+.

**What's done:**
- Core working memory files (CLAUDE.md, TASKS.md).
- Traveler profiles (you & Alex with constraints + preferences).
- Project overview + strategic decisions framework.
- Glossary (Japanese terms, currency, abbreviations).
- Master itinerary template (populated with options, dates/details TBD).
- Destination guide (weather, money, customs, accessibility).
- City selection research (4 candidates analyzed, 3 scenarios outlined).
- Flight research framework (85K miles strategy, scenarios, CPP analysis).

**What's pending:**
- Confirm trip dates (which week in October?).
- Select cities (2 or 3? Kyoto + Tokyo / Kyoto + Osaka + Tokyo / Kyoto + Kobe?).
- Research & book flights, hotels, activities, restaurants.
- Write printable guides (language phrasebook, packing lists, itinerary PDF).

---

## How to Use This Project

### For Each Session

**Start here:**
1. Read `TASKS.md` — see what's active, what's done.
2. Read the "Last session" log in `CLAUDE.md` (Trip Planning Status).
3. Choose your focus task(s) for this session.

**Core reference files (use often):**
- `memory/itinerary.md` — Day-by-day schedule, accommodation, budget (THE operational source of truth).
- `memory/projects/japan-trip.md` — Strategic decisions, trip philosophy, booking details.
- `memory/people/you.md` & `memory/people/alex.md` — Preferences, health notes, constraints.
- `memory/glossary.md` — Japanese terms, currency, abbreviations, airport codes.

**Guides (read per-city as needed):**
- `memory/guides/destination-guide.md` — General Japan info (weather, money, customs, accessibility).
- `memory/guides/city-name-activities.md` — (Created when city is finalized) Day-by-day temples, gardens, schedule.
- `memory/guides/city-name-restaurants.md` — (Created when city is finalized) Restaurant picks, day-by-day, vegetarian/omnivore options.
- `memory/guides/language-guide.md` — (To be created) Japanese phrasebook by situation.

**Research files (read when you need the underlying data):**
- `memory/research/city-selection.md` — Why Kyoto, Tokyo, Osaka, or Kobe? 3 scenarios analyzed.
- `memory/research/flight-research.md` — 85K miles strategy, award availability, CPP analysis.
- `memory/research/hotel-research-{city}.md` — (TBD) Shortlists, reviews, rates.
- `memory/research/restaurant-research-{city}.md` — (TBD) Vegetarian + omnivore options, reviews.
- `memory/research/activity-research-{city}.md` — (TBD) Temples, gardens, entry fees, accessibility.
- `memory/research/transport-research.md` — (TBD) JR Pass vs. point-to-point, metro systems.

**Session logs (read the latest):**
- `memory/sessions/session-log-{date}.md` — Created at end of each session. Documents decisions, research, progress.

### At the End of Each Session

1. **Update TASKS.md** — Mark completed tasks as Done. Move new discoveries to Active or Pending.
2. **Update CLAUDE.md** — Update "Last session" pointer + Trip Planning Status.
3. **Write session log** — Create `memory/sessions/session-log-{date}.md` with:
   - Date, session focus, decisions made.
   - Files created/updated.
   - Progress toward major milestones.
   - Blockers or next steps.

### Handoff Between Sessions

**Primary handoff mechanism:** Session logs in `memory/sessions/`. Read the latest log before starting a new session.

**Do NOT rely on OpenMemory for session-to-session continuity.** On-disk files (CLAUDE.md, TASKS.md, memory/) are the source of truth.

---

## Key Constraints & Preferences (Quick Reference)

| Constraint | Impact | Mitigation |
|-----------|--------|-----------|
| **You are vegetarian** | Restaurant research must present both vegetarian & omnivore options equally. Frame as "here's a temple cuisine option" not "vegetarian meal." | Research shojin ryori, Buddhist temple food. Mix in vegetable-forward ramen, sushi. |
| **Alex's knee (pain after long days of standing/stairs)** | Plan 1 main activity/day, mid-day breaks, elevator/low-stair hotels, taxis for tired evenings. | Don't over-medicalize. Alex walks fine, just manage comfort. Research stairs/elevators beforehand. |
| **Both nervous about language barrier** | Build strong phrasebook (restaurant, hotel, transit, emergencies). Learn key phrases. Set up translation app. | Consider guided activity on Day 1 to build confidence. Hotel staff usually English-competent. Photo-pointing strategy. |
| **$8,000 total budget** | Allocate: flights ($0–2K with miles), hotels ($2–2.5K), meals ($2.5–3K), activities ($400–600), transport ($300–500), contingency ($600–1K). | 85K United miles may cover flights fully → budget flexibility for experience quality. |
| **10 days, 2–3 cities, depth over breadth** | 1 main activity/day. No rushing between sites. Time for temple gardens, food exploration, rest. | Pick 2 cities (Kyoto + Tokyo) or 3 if willing to move faster (Kyoto + Osaka + Tokyo). |

---

## Important Notes on Project Management

**From past trip planning experience:**

1. **Close parent tasks when last subtask finishes.** Don't wait to be asked. Move to Done immediately.
2. **Session logs are primary handoff.** Not OpenMemory summaries. Files are source of truth.
3. **Every assertion needs backing action.** Don't say "I'll remember that" — save to memory/disk.
4. **Research validation:** Use Web Search to cross-check Perplexity or any single source. Don't rely on one source.
5. **Present full range in research.** Don't pre-filter by cost or other criteria — show options, let you decide.
6. **Avoid tourist traps.** Flag mediocre restaurants in high-traffic zones, even if well-rated.
7. **Day-by-day framing.** Slot activity/restaurant options into the schedule, not abstract categories.
8. **Accessibility framing:** Don't over-medicalize Alex. Note stairs/elevators factually in planning, but don't highlight in shared materials unless critical. Alex is fine, just managing comfort.

---

## What Comes Next (Sessions 1–3)

### Session 1: Research & Discovery
- **Confirm dates** (which October week?).
- **Select cities** (Scenario A, B, or C?).
- Research flights, hotels, activities, restaurants per selected cities.
- Build shortlists, analyze options, present findings.

### Session 2: Booking & Logistics
- **Book flights** (85K miles or cash decision).
- **Book accommodation** (3–4 per city, confirm elevator/low-stair).
- **Book major activities** (if limited inventory, e.g., guided tours).
- Build language phrasebook PDF.

### Session 3+: Finalization & Prep
- **Write itinerary PDF** (shared with you & Alex — day-by-day schedule, phone numbers, language tips).
- **Build packing lists** (you + Alex, markdown checklists).
- **Finalize restaurant reservations** (confirm day-by-day picks).
- **Last-minute confirmations** (travel insurance, weather check, contingency plans).

---

## File Structure (Full Directory Map)

```
outputs/
├── CLAUDE.md                          # Working memory (this is you as a planner)
├── TASKS.md                            # Active task tracker
├── README.md                           # This file
│
├── memory/
│   ├── people/
│   │   ├── you.md                      # Your profile, preferences, constraints
│   │   └── alex.md                     # Alex's profile, knee notes, language anxiety
│   ├── projects/
│   │   └── japan-trip.md               # Strategic decisions, trip philosophy, budget
│   ├── itinerary.md                    # Master day-by-day schedule (THE source of truth)
│   ├── glossary.md                     # Japanese terms, currency, airport codes
│   ├── guides/
│   │   ├── destination-guide.md        # Weather, money, customs, accessibility (all cities)
│   │   ├── city-name-activities.md     # (TBD, per city) Day-by-day temples, gardens
│   │   ├── city-name-restaurants.md    # (TBD, per city) Restaurant picks, vegetarian/omnivore
│   │   ├── language-guide.md           # (TBD) Japanese phrasebook by situation
│   │   └── accessibility-guide.md      # (TBD) Stairs, elevators, rest spots, taxi tips
│   ├── research/
│   │   ├── city-selection.md           # 4 cities analyzed, 3 scenarios
│   │   ├── flight-research.md          # 85K miles strategy, CPP analysis
│   │   ├── hotel-research-{city}.md    # (TBD, per city) Shortlists, reviews, rates
│   │   ├── restaurant-research-{city}.md # (TBD, per city) Full research, narrowed picks
│   │   ├── activity-research-{city}.md # (TBD, per city) Temples, gardens, fees
│   │   └── transport-research.md       # (TBD) JR Pass vs. point-to-point, metro systems
│   └── sessions/
│       └── session-log-{date}.md       # (TBD, one per session) Session summary, decisions, progress
│
└── (shareable outputs TBD)
    ├── Itinerary — You & Alex.pdf      # Mom-facing PDF itinerary (scheduled for Session 3)
    ├── Language Guide.pdf               # Printable phrasebook (scheduled for Session 2)
    ├── Packing List — You.md           # Your packing checklist (scheduled for Session 3)
    └── Packing List — Alex.md          # Alex's packing checklist (scheduled for Session 3)
```

---

## Questions? Start With These Files

- **What's the trip philosophy?** → `memory/projects/japan-trip.md`
- **What are the constraints?** → `memory/people/you.md` + `memory/people/alex.md`
- **What are the city options?** → `memory/research/city-selection.md`
- **What about the 85K miles?** → `memory/research/flight-research.md`
- **What do I need to pack?** → `memory/guides/destination-guide.md` (packing section)
- **How do I speak Japanese?** → `memory/guides/language-guide.md` (to be created)
- **What's the day-by-day schedule?** → `memory/itinerary.md`

---

**Created:** March 31, 2026 (Session 0 — Scaffold)
**Status:** Ready for Session 1 (research & discovery)
**Next:** Confirm dates + select cities, then begin research.
