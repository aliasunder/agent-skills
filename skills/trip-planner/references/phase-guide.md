# Phase Guide

Detailed guidance for each planning phase. Read the relevant section when entering a new phase or when you're unsure what "done" looks like.

## Phase 1: Define — Laying the Foundation

### What good looks like
- Traveler profiles in `memory/people/` capturing: health situation (specific, not generic — how does it manifest day-to-day?), travel anxiety level, interests, aesthetic preferences, dietary needs and strictness, non-negotiables
- Project file in `memory/projects/` with trip overview, budget, constraints
- CLAUDE.md populated with travelers, dates, budget, preferences, file map
- TASKS.md set up with proper format (see `references/tasks-and-dashboard.md`) and seeded with Phase 2 tasks
- Dashboard ready if the Productivity plugin pattern is being used
- Project-wide conventions established:
  - Home currency and temperature units
  - Each traveler's dietary needs (specific — not just "vegetarian" but: includes eggs? Fish? How strict?)
  - Loyalty programs, points balances, and minimum acceptable redemption value
  - **Booking philosophy**: Does the traveler prioritize flexibility (refundable everything, premium options for comfort, willing to pay more for cancellation protection)? Or optimize for cost? Or somewhere in between? This shapes every booking decision across the entire trip.
  - Booking preferences: direct sites vs. OTAs, premium cabins vs. economy, flexibility vs. price
  - Communication style for materials (formal vs. casual, how much detail)

### Common pitfalls
- **Rushing past profiles.** Traveler profiles are the most important input to every future decision. Ask follow-ups: "You mentioned back pain — how does that affect a typical day of walking? What makes it worse vs. better? What's your threshold before you need to rest?"
- **Not capturing anxiety.** A first-time international traveler needs different materials, more buffer time, and more backup plans than a frequent flyer.
- **Generic dietary labels.** "Vegetarian" isn't enough. And if only one traveler has dietary restrictions, don't frame all recommendations around that restriction — the other traveler's food preferences matter equally.
- **Not establishing booking philosophy.** If you don't ask, you'll default to mid-range assumptions and the user will have to correct you repeatedly. Some travelers want refundable everything and premium cabins; others want the cheapest option that works. Ask upfront.

### Transition to Phase 2
You're ready when you could describe each traveler to a local guide and they'd know exactly what to recommend.

## Phase 2: Route — Deciding the Shape

### What good looks like
- 2-4 route options presented with clear trade-offs
- **Decision-support materials** created if the user needs to discuss with travel companions (route comparison PDFs, early itinerary previews)
- User has chosen a route with documented reasoning
- Itinerary skeleton in `memory/itinerary.md`
- Rejected options archived in `archive/`

### How to compare routes
Build a comparison that makes trade-offs visible — what each route *feels* like, not just the logistics. "Option A is a slower, deeper experience — you'll really get to know each place. Option B covers more ground but means more travel days."

### Common pitfalls
- **Too many cities.** Each city needs 2+ full days minimum. Travel days eat into the trip. For 2 weeks, 3-4 cities is usually right.
- **Ignoring travel days.** A "5-hour train" means losing most of a day. Build these in explicitly.
- **Not creating sharable materials.** If the user is deciding with a travel companion, offer to create a comparison document they can share. An excited-but-anxious companion needs a warm, visual preview — not a logistics table.

### Transition to Phase 3
Ready when the user has committed to a route and you have a date-by-date skeleton. Don't start booking until the route is firm.

## Phase 3: Transport — Booking the Bones

### Ordering
1. **Flights first** — lock trip dates and constrain everything else.
2. **Inter-city transport** — trains, buses, or flights between cities.
3. **Arrival transfers** — airport/station to hotel (some may wait for Phase 4 when hotels are confirmed).

### Booking workflow
1. Research options (read `references/research-methodology.md`)
2. Present comparison to user with trade-offs
3. **Apply the booking philosophy** — if the user prioritizes flexibility, highlight refundable fares and premium options. If optimizing for cost, lead with value.
4. Book (offer to navigate complex booking sites via browser tools if available)
5. **Check email for confirmation** — extract reference numbers, seat assignments, fare conditions, cancellation policies
6. Update project file and budget

### Common pitfalls
- **Forgetting luggage policies.** Train luggage rules vary wildly. Check and document.
- **Tight connections without backup.** Note the next available service for every connection.
- **Not checking loyalty program value.** Calculate value-per-point before using points. Check if cash is cheaper.
- **Not verifying bookings via email.** Confirmation emails often contain details (seat numbers, platform info, fare rules) not shown during the booking flow.

### Transition to Phase 4
Ready when all inter-city transport is booked and you know arrival/departure times for each city.

## Phase 4: Accommodation — Finding Home Bases

### Per-city research approach
Research one city at a time. Complete the full research cycle for each before moving to the next. Apply the booking philosophy — if the user wants refundable, prioritize free cancellation. If they want premium comfort, weight that in the comparison.

### Common pitfalls
- **Prioritizing price over location.** A hotel 20 minutes from center by transit becomes exhausting for travelers with physical constraints who need to get home after long days.
- **Making blanket accessibility rules.** Don't assume what is or isn't a dealbreaker. Read the profile. "Prefers lower floors when possible" is different from "requires ground floor." Each traveler's situation is specific.
- **Booking through OTAs when direct is available.** Direct booking usually means easier changes and sometimes better rates.

### Transition to Phase 5
Ready when every night has a booked hotel and you know the exact locations.

## Phase 5: Activities — Building the Days

### Approach
Read traveler profiles. Activities should match interests, energy, and physical situation. The goal is experiences they'll remember, not a checklist.

### Common pitfalls
- **Overscheduling.** One main activity per day for relaxed trips. Include genuine rest time.
- **Forgetting rest days.** After 3-4 active days, build in unstructured time.
- **Mixing restaurants into activity guides.** Keep them separate. Restaurant decisions come in Phase 6.
- **Not noting timing constraints.** Check day-of-week closures, seasonal hours, timed entry requirements.

### Transition to Phase 6
Ready when every day has a plan and you know where travelers will be at roughly what times.

## Phase 6: Restaurants — Feeding the Trip

### The research → guide pipeline
This phase follows a strict pipeline:
1. **Research file** — broad discovery, cross-validation, comparison table, ruled-out table. Saved to `memory/research/restaurant-research-{city}.md`.
2. **Inline presentation** — present top picks to the user **in day-by-day context**, slotted into the actual schedule. This happens in the chat, not as a separate file.
3. **User discussion** — the user reviews, adjusts, approves.
4. **Guide file** — only after approval, create `memory/guides/{city}-restaurants.md` with the final picks.

### Common pitfalls
- **Recommending by proximity to attractions alone.** The restaurant next to the cathedral is often a tourist trap. Walk further for quality.
- **Not checking recent reviews.** Check the last 6 months — restaurants change hands.
- **Creating the guide before getting user input.** The guide is a record of decisions, not a proposal.

### Transition to Phase 7
Ready when every meal has a primary and backup option and necessary reservations are made.

## Phase 7: Pre-Trip Prep — Making It Real

### What needs to be produced
Read `references/materials-guide.md` before starting. Common deliverables:
- Itinerary guide (narrative PDF)
- Daily cards (one page per day, printed)
- Packing lists (per traveler)
- Language cheat sheet
- Companion app (optional)

### Common pitfalls
- **Building before the itinerary is finalized.** Any schedule change means redoing materials.
- **Incremental script editing.** For PDF generators, rewrite from scratch if more than ~10 edits are needed.
- **Not testing outputs.** Always open/read the generated file before presenting.

### Transition to Phase 8
Ready when all materials are produced, reviewed, and approved by the user.

## Phase 8: Final Check — Ready to Go

### Checklist
- All flights confirmed (check airline status)
- All train tickets downloaded/accessible
- All hotel bookings confirmed (check for pending payments)
- All activity tickets/vouchers downloaded
- All restaurant reservations confirmed
- Travel insurance confirmed for all travelers
- TASKS.md has no unresolved active items
- Budget reconciled (actual vs. planned)
- Emergency info accessible to all travelers
- All materials printed or downloaded for offline access
- Old/cancelled bookings cleaned up (refunds received or waived)
- Email checked for any last-minute changes or updates

### Transition to departure
A clean TASKS.md with everything in Done, a current budget summary, and confidence that everything is booked, confirmed, and accessible. The trip is ready.
