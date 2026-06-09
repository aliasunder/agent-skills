# Memory

## Me
User + Alex — planning a 10-day trip to Japan in October 2026. Deep dive in 2-3 cities, food-focused, temples and gardens. First time in Asia; Alex is nervous about the language barrier. Budget: ~$8K USD, 85K United miles.

## People
| Who | Role |
|-----|------|
| **User** | Trip planner, partner, vegetarian |
| **Alex** | Partner, travel companion, first-time Asia traveler |

→ Full profiles: memory/people/user.md, memory/people/alex.md

## Terms
| Term | Meaning |
|------|---------|
| Points | United Airlines miles (85,000 available) |
| CPP | Cents per point — measures points redemption value |
| Shinkansen | Japanese bullet train (high-speed rail) |

→ Full glossary: memory/glossary.md

## Projects
| Name | What |
|------|------|
| **Japan Trip Oct 2026** | 10-day trip, October 2026. Deep dive 2-3 cities, food + temples + gardens. Budget: ~$8K USD + 85K United miles. **Phase: 1 (Define).** |

→ Full details: memory/projects/japan-trip-oct2026.md

## Trip Planning Status (Apr 2, 2026)
- **Phase:** 1 (Define) — Establishing travelers, dates, budget, preferences, booking philosophy
- **Last session:** None yet — first session (scaffolding)
- **Trip dates:** October 2026 (exact dates TBD)
- **Duration:** 10 days
- **Budget:** USD $8,000 total (both travelers); 85,000 United miles
- **Cities:** TBD — Phase 2 will decide on 2-3 main cities
- **Booked so far:** Nothing yet

## Preferences

**Currency & Units:**
- Currency: USD
- Temperature: Fahrenheit (assumed)

**Dietary:**
- User: Vegetarian (eggs/dairy OK, not strict in limited-option situations)
- Alex: Omnivore, loves food
- Restaurant recs should serve both well, not vegetarian-first

**Loyalty Programs:**
- United Airlines: 85,000 miles (combined)
- Minimum acceptable CPP: ~1.5 (don't redeem below this threshold)

**Booking Philosophy (Balanced Approach):**
- Prioritizes flexibility and peace of mind over absolute lowest cost
- Willing to pay for refundable/flexible options when reasonable
- Mid-range to upscale hotels preferred (3-4 stars, character-filled)
- Direct hotel bookings preferred when available
- Comfortable with both cash and points for flights

**Communication Style:**
- Warm, conversational materials
- Context and explanation (not just logistics)
- Clear guidance (especially reassuring for Alex's first Asia trip)
- Maps, directions, practical how-to info

## File Map

### Root — Core working docs
| Path | When to read | What |
|------|-------------|------|
| `CLAUDE.md` | **Session start** — agent working memory | Me, people, terms, projects, trip snapshot, preferences, file map |
| `TASKS.md` | **Session start** — active task tracker | What's active, waiting on, next, and done |
| `dashboard.html` | **Visual task management** | Kanban board for TASKS.md (optional, standalone) |

### memory/ — Core reference (read often)
| Path | When to read | What |
|------|-------------|------|
| `memory/projects/japan-trip-oct2026.md` | **Phase 1, booking, or trip structure questions** | Strategic decisions, full booking details (when booked), budget status, constraints |
| `memory/people/user.md` | **Activity or material planning** | User's dietary needs, interests, travel style, communication preferences |
| `memory/people/alex.md` | **Activity/comfort/anxiety decisions** | Alex's knee constraint, language anxiety, interests, health details |
| `memory/glossary.md` | **Unfamiliar abbreviations** | Trip shorthand, terms, airport codes |
| `memory/itinerary.md` | **Phase 2+** — day-by-day schedule | Created after route is decided; the operational single source of truth |

### memory/sessions/ — Cross-session handoff
| Path | What |
|------|------|
| `memory/sessions/` | Session logs created at end of each session |

### memory/research/ — Planning research (read when needed)
| Path | When to read | What |
|------|-------------|------|
| `memory/research/{topic}-{city}.md` | **During research phases** | Raw research, comparisons, options evaluated |

### memory/guides/ — Curated per-city guides (read during activity planning)
| Path | When to read | What |
|------|-------------|------|
| `memory/guides/{city}-activities.md` | **Phase 5+** | After user approves research, curated activity guide |
| `memory/guides/{city}-restaurants.md` | **Phase 6+** | After user approves restaurant research, curated picks |

### memory/plans/ — Implementation plans
| Path | When to read | What |
|------|-------------|------|
| `memory/plans/{deliverable}.md` | **Before building complex outputs** | Planning for PDF generation, packing lists, companion apps, etc. |

### outputs/ — Traveler-facing artifacts
| Path | What |
|------|------|
| `outputs/` | PDFs, guides, daily cards, packing lists, language sheets, decision-support docs (created during any phase as needed) |
| `outputs/tickets/` | Downloaded e-tickets and vouchers |
| `outputs/receipts/` | Purchase receipts |

### archive/ — Superseded options
| Path | What |
|------|------|
| `archive/` | Rejected routing options, old research, cancelled bookings (moved here with explanation) |

## Open Questions

1. **Booking philosophy confirmed?** Assumed balanced (flexibility + comfort valued, willing to spend), but confirm with user if needed.
2. **Exact trip dates?** October 2026, but which weeks? Affects flights and hotel availability.
3. **Points strategy:** Use United miles for both flights? Or split with cash? Preference on cash vs. points?
4. **Cities decision:** Which 2-3 cities for Phase 2? Tokyo + Kyoto mandatory? Add Osaka, Hiroshima, others?

## Resolved

(None yet — first session)
