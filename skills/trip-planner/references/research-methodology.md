# Research Methodology

A systematic process for evaluating any travel option — accommodation, restaurants, activities, or transport. Follow these 6 phases in order. Don't skip the cross-validation step; single-source research leads to bad recommendations.

## The Research → Guide Pipeline

This is important: research files and guide files serve different purposes and are created at different times.

- **Research file** (`memory/research/`) — Created first. Broad, comparative, all options considered. Contains the full comparison table, ruled-out table, sources consulted. This is the evidence.
- **Inline recommendation** — After the research file exists, present your top recommendations to the user *within the session*, organized in the context they'll be used (day-by-day for restaurants/activities, by city for hotels). This is the discussion.
- **Guide file** (`memory/guides/`) — Created last, only after the user has reviewed recommendations and made decisions. This is the curated verdict — the final picks, organized for easy reference during the trip. It is NOT a reformatted copy of the research file.

Never create a guide before the user has weighed in on the research. The guide represents decisions made, not options proposed.

## Phase 1: Scope & Criteria

Before searching, define what you're looking for:

- **Category**: What are you researching?
- **Hard filters**: Non-negotiable requirements that eliminate options immediately. Pull these from traveler profiles in `memory/people/`.
- **Nice-to-haves**: Preferences that differentiate between qualifying options.
- **Budget range**: If the user has provided one. Don't use budget as a hard filter during research — present the full range and let the user decide.
- **Star/quality preference**: If stated (e.g., "3-4 star"), use as the primary filter but don't rigidly exclude properties above that range when they're price-competitive with options in range. A 5-star property at a 4-star price point is worth including — flag it with a note like "Above stated range but price-competitive at $X/night." Most people won't turn down something better than what they asked for at the same price.

On hard filters: be careful about what you treat as non-negotiable vs. what's a preference. "Must have vegetarian options" for a vegetarian traveler is a hard filter. "Has an elevator" for a traveler with back pain might be a strong preference but not a hard filter — check the traveler profile for specifics about their situation.

## Phase 2: Broad Discovery

Cast a wide net. Aim for 15-25 initial candidates from multiple sources:

- **AI search** (Perplexity or similar): Good for an initial list with context. Treat results as leads, not facts.
- **Web search**: Search for "[category] in [city] [year]" for recent articles, guides, and forum discussions.
- **Official tourism sites**: City tourism boards often have curated lists.
- **Booking platforms**: For hotels, check the hotel's own site, Booking.com, Google Maps. For restaurants, check Google Maps and local review platforms.

Record every candidate with: name, location, initial rating, source, and a one-line note.

## Phase 3: Cross-Validation

The most important phase. For each candidate that passes hard filters:

- **Check ratings across 2-3 sources.** Inconsistent ratings (4.8 on one site, 3.5 on another) are a red flag.
- **Read recent reviews** (last 6-12 months). Patterns matter more than individual reviews.
- **Verify practical details**: Hours, seasonal closures, booking requirements, dietary accommodation.
- **Check location on a map** relative to the traveler's hotel and planned activities. "Central" is subjective.

Flag anything unverifiable as "needs confirmation" — the user may need to call or email.

## Phase 4: Structured Comparison

Build a comparison table with columns consistent for the category:

**Hotels:**
| Name | Location | Rating | Room Situation | Style | Breakfast | Cancellation | Price/Night | Notes |

**Restaurants:**
| Name | Cuisine | Rating | Dietary Options | Price Range | Booking Method | Proximity | Notes |

**Activities:**
| Name | What | Duration | Physical Demand | Cost | Booking Required? | Rain Alternative? | Notes |

**Transport:**
| Option | Route | Duration | Cost | Luggage Policy | Flexibility | Notes |

Note: Column names should reflect the actual project conventions (e.g., "Dietary Options" with relevant dietary needs from traveler profiles — don't hardcode specific diets as column names). Price columns should use the project's home currency as established in Phase 1.

Present the table with brief commentary highlighting trade-offs between top options.

## Phase 5: Ruled-Out Table

Document what you eliminated and why. This prevents revisiting dead ends:

| Name | Category | Why Ruled Out |
|------|----------|---------------|
| Example Hotel | Accommodation | 4th floor, no elevator, traveler prefers lower floors |
| Example Restaurant | Dining | Closed Sundays (conflicts with our schedule) |

Save this in the research file.

## Phase 6: Recommendation

Produce a shortlist of 3-6 options with clear reasoning. For restaurants and activities, present recommendations **in day-by-day context** — slotted into the actual schedule so the user can see how picks work with planned activities, not just as a standalone ranked list.

- **Top pick** and why
- **Runner-up** and what trade-off it represents
- **Backup** in case the top pick doesn't work out

Present this to the user **inline in the session** for discussion. Wait for their approval or modifications before creating the guide file.

For major decisions (hotels, flights), this is a full discussion — present trade-offs and let the user choose.

## Category-Specific Notes

### Accommodation
- Book on the hotel's direct site when possible
- Check: room situation (floor, elevator, bed config), A/C, breakfast, noise reviews
- Traveler comfort preferences are specific to each person — read the profile rather than applying generic rules
- Cancellation policy matters — free cancellation with reasonable notice is strongly preferred

### Restaurants
- Present in day-by-day view slotted into the schedule, not just by category
- Check for tourist trap signals: prime location near major attraction + mediocre ratings + generic menu
- Verify dietary accommodation for each traveler's specific needs (check menus or reviews, don't assume)
- Note booking method clearly: online (with link), phone only, walk-in, reservation not needed
- Keep restaurant research and guides in **separate files** from activity research and guides

### Activities
- Read traveler profiles before recommending anything
- One main activity per day for relaxed-pace trips — explain *why* each activity matters, not just *what* it is
- Include rain/bad-weather and low-energy alternatives
- Check day-of-week constraints (closures, limited hours)
- Note timed entry requirements and advance booking deadlines

### Transport
- Compare modes on: time, cost, luggage ease, flexibility, accessibility
- Check fare classes, refund policies, seat selection
- For loyalty program redemptions, calculate value (e.g., cents-per-point) — don't waste points on poor redemptions
- Have a backup plan for tight connections
- After booking, check email for the confirmation and extract all relevant details

## Research File Organization

Save research to `memory/research/{topic}-{city}.md` (e.g., `restaurant-kyoto.md`, `hotel-paris.md`). The directory is already `research/`, so don't repeat "research" in the filename. Include:
- Date of research
- Sources consulted
- Full comparison table
- Ruled-out table
- Recommendation with reasoning

Only create the guide (`memory/guides/{city}-{type}.md`) after the user has reviewed and approved the recommendations.
