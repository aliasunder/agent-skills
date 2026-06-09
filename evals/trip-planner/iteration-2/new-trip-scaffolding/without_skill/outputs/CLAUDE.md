# Memory

## Me
You and Alex are planning a shared 10-day trip to Japan in October 2026. This is both of your first time in Asia. October is shoulder season — mild weather, fewer crowds than summer/cherry blossom, still good for temples and gardens.

## People
| Who | Role |
|-----|------|
| **You** | Planning partner, vegetarian. Loves food, temples, gardens. |
| **Alex** | Planning partner, eats everything. Bad left knee from old soccer injury — walks fine but stairs become painful after long days of activity. Nervous about language barrier. |

→ Full profiles: `memory/people/you.md` + `memory/people/alex.md`

## Trip Essentials
| Item | Value |
|------|-------|
| **Duration** | 10 days (October 2026 — exact dates TBD) |
| **Budget** | $8,000 USD total (flights + accommodation + food + activities + transport) |
| **Frequent Flyer** | 85,000 United miles (untapped resource) |
| **Style** | Deep over shallow — 2–3 cities max, not rushing. Food, temples, gardens. |
| **Constraints** | Vegetarian dining (you), stairs/long standing (Alex), language barrier (both), first time in Asia |

→ Full project details: `memory/projects/japan-trip.md`
→ Glossary: `memory/glossary.md`

## Trip Planning Status (Mar 31, Session 0)
- **Phase:** Scaffold + discovery. Project structure created. Awaiting: city selection, date confirmation, flight strategy.
- **Dates:** October 2026 (exact dates TBD)
- **Cities:** 2–3 candidates under consideration (Kyoto, Tokyo, Osaka, Kobe likely). TBD.
- **Itinerary:** Not yet sketched
- **Flights:** Not researched (85K United miles available — may offset cost significantly)
- **Accommodation:** Not researched
- **Activities:** Not researched (but temple + garden + food priorities known)
- **Restaurants:** Not researched
- **Transport:** Not researched
- **Budget breakdown:** Unallocated

## Preferences
- **Activity style:** Depth over breadth — one main activity per day, unhurried pace. Temples and gardens (you interested, Alex likely interested). Food-focused (both). Avoid packed tourist experiences (e.g., Arena opera photo negative reaction precedent doesn't apply here, but signals quality-of-experience preference).
- **Food:** You are vegetarian; Alex eats everything. Present both options naturally, not vegetarian-first framing.
- **Accommodation:** Mid-range 3–4 star hotels or traditional ryokans with local character. Elevator/ground floor OR low-stair access preferred (Alex's knee). Free cancellation (7+ days).
- **Transport:** Taxis, transit, trains — no driving. Minimal stairs/long standing for Alex.
- **Language:** Both nervous. Need phrasebook + logistical language (restaurant, hotel, transit) + photo-communication fallbacks.
- **Accessibility:** Don't over-medicalize — Alex walks fine, just manage stairs and standing. Plan activities with mid-day breaks, avoid all-day hikes. No elevators required, but note them in guides.

## Agent Workflow Notes

### General Approach
- **Always check TASKS.md first** at the start of each session for blockers and priorities.
- **Read the "Last session" log** for cross-session context (start from bottom of TASKS.md or memory/sessions/).
- **Core reference files:** `memory/itinerary.md`, `memory/projects/japan-trip.md`, `memory/people/alex.md`, `memory/people/you.md`.
- **Document everything on disk** — every session with TASKS.md progress must end with a session log in `memory/sessions/session-log-{date}.md`.
- **Update "Last session" pointer** in Trip Planning Status (above) at the end of every session.

### Known Patterns (from Tanisha's feedback)
- **Close parent tasks when last subtask completes** — don't wait to be asked.
- **Session logs are the primary handoff** — not OpenMemory. Use on-disk files.
- **Every assertion needs backing action** — don't say "I'll remember" without saving to memory.
- **Research validation:** Use Web Search to cross-check Perplexity results. Don't rely on single sources.
- **Present full range in research** — don't pre-filter by cost.
- **Avoid tourist traps** — flag mediocre restaurants in high-traffic zones.
- **Day-by-day framing** — slot options into the schedule, not just by category.
- **No over-medicalization** — comfort framing, not mobility aids.
- **Accessibility framing:** Don't flag elevators/stairs/ramps in mom-facing materials. Alex is fine — Tanisha planned around comfort but prefers it not be highlighted.

## File Map

### Root — Core working docs
| Path | What |
|------|------|
| `CLAUDE.md` | This file — agent working memory |
| `TASKS.md` | Active task tracker — **start here each session** |

### memory/ — Core reference
| Path | When to read | What |
|------|-------------|------|
| `memory/itinerary.md` | **Most tasks** — the operational single source of truth | Day-by-day schedule, accommodation, transport, meals, budget, Charged? column |
| `memory/projects/japan-trip.md` | **Booking, payment, structure decisions** | Strategic decisions + rationale, booking details, payment status |
| `memory/people/you.md` | **Activity/comfort decisions** | Your preferences, dietary needs, interests |
| `memory/people/alex.md` | **Activity/comfort decisions** | Alex's health notes, knee accommodation, interests, anxieties |
| `memory/glossary.md` | **Unfamiliar abbreviations** | Terms, airport codes, currency, shorthand |

### memory/sessions/ — Cross-session handoff
| Path | What |
|------|------|
| `memory/sessions/session-log-{date}.md` | Session logs — created at end of each session with TASKS.md progress |

### memory/guides/ — Trip reference docs
| Path | What |
|------|------|
| `memory/guides/destination-guide.md` | City primer — character, weather, mobility notes, tipping, cash/cards, packing advice for all cities |
| `memory/guides/city-name-activities.md` | Per-city day-by-day schedule, booking checklist |
| `memory/guides/city-name-restaurants.md` | Per-city restaurant guide — day-by-day picks, booking links |
| `memory/guides/language-guide.md` | Japanese phrases by situation + city-specific needs |
| `memory/guides/accessibility-guide.md` | Stairs, elevators, rest spots, metro accessibility, taxi tips (Alex-focused, non-medicalized) |

### memory/research/ — Planning research
| Path | What |
|------|------|
| `memory/research/flight-research.md` | United miles pricing, routing options, award availability |
| `memory/research/city-selection.md` | Candidate cities analyzed, rationale for final selection |
| `memory/research/hotel-research-{city}.md` | Per-city hotel shortlists and analysis |
| `memory/research/restaurant-research-{city}.md` | Per-city restaurant research — vegetarian + omnivore options |
| `memory/research/activity-research-{city}.md` | Per-city temples, gardens, museums, cultural sites |
| `memory/research/transport-research.md` | Japan Rail Pass vs. point-to-point, JR vs. local lines, metro systems |

### outputs/ — Shareable artifacts
| Path | What |
|------|------|
| `outputs/Itinerary — You & Alex.pdf` | Mom-facing itinerary PDF (when ready) |
| `outputs/Language Guide.pdf` | Printable Japanese phrasebook |
| `outputs/Packing List — You.md` | Your packing checklist |
| `outputs/Packing List — Alex.md` | Alex's packing checklist (knee-comfort focused) |

---

**Created:** March 31, 2026 (Session 0 — scaffold)
**Last Updated:** March 31, 2026
**Last Session:** (This is the first session — no prior log yet)
