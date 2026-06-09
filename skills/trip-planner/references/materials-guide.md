# Materials Production Guide

How to produce traveler-facing deliverables and decision-support documents. This guide covers both final materials (Phase 7) and mid-process decision aids (any phase).

## Two Types of Materials

### Decision-Support Materials (Any Phase)
Created whenever the user needs to share options or progress with travel companions. These facilitate discussion and decisions. They don't need to be as polished as final materials, but should be warm and clear — especially if the travel companion is anxious about the trip or unfamiliar with the destinations.

Examples: route comparison documents, option summaries, early itinerary previews, hotel shortlists.

### Final Traveler Materials (Phase 7+)
Created after the itinerary is finalized. These are the polished, self-contained documents travelers carry with them. Each should work on its own without referencing other documents.

## General Principles

**Audience awareness:** Materials are for travelers, not planners. Frame everything positively and personally. "We picked this hotel because it's charming and perfectly located" — not referencing constraints that drove the decision.

**Accessibility framing:** If travelers have comfort considerations, the planner likely already accounted for them. Don't flag accessibility details (elevators, ramps, distances) in traveler-facing materials unless the planner specifically asks. The traveler should feel the trip was planned with joy.

**Self-contained:** Each material should work independently. A daily card has everything needed for that day. The itinerary guide works as a standalone overview.

**Offline-first:** Travelers may not have reliable internet. PDFs and printed materials are primary. Digital companions should work offline.

**Use project conventions:** Temperature units, currency, time format — use whatever was established in Phase 1 and stored in CLAUDE.md, not hardcoded defaults.

## Material Types

### Itinerary Guide (PDF)

A narrative overview of the entire trip for reading before departure.

**Structure:**
- Cover page with trip title, dates, a warm opening
- Trip overview with route summary, city-by-city preview
- Per-city sections: character/vibe, weather, what to wear, hotel details, day-by-day activities, restaurant picks with addresses, key local phrases, practical tips
- Closing: something warm and personal

**Tone:** Warm, conversational, "we/our" framing. Short paragraphs, no jargon.
**Format:** PDF, screen-optimized. Clickable links for restaurants and bookings.
**Length:** 1-2 pages per city plus overview and closing.

### Daily Cards (PDF, printed)

One page per day — a quick-reference card for the bag.

**Each card includes:**
- Date, day of week, city, weather forecast
- Schedule table (time → activity → location)
- Transport details if traveling (departure, platform, seats, reference)
- Restaurant picks (name, address, reservation time)
- Emergency info: both travelers' phone numbers, local emergency number
- Rain/low-energy alternative plan

**Format:** PDF, letter size, black and white for easy printing.
**Tone:** Practical, concise, tables over prose, "we/our" framing.

### Language Cheat Sheet (PDF, printed)

Essential phrases organized by situation, one page per language.

**Structure per page:** Language name and cities it applies to. Organized by situation: essentials, restaurant, market/shopping, getting around, nice-to-know, emergency. Pronunciation hints where non-obvious.

**Format:** PDF, letter size, B&W. 1-2 pages total.

### Packing Lists (Markdown)

Separate per-traveler checklists tailored to climate and activities.

**Structure:** Documents, medications/health, clothing, shoes, toiletries, electronics/adapters, reminders.
**Format:** Markdown with checkboxes (`- [ ]`). Shared items get "Coordinate with [name]" tags.
**Tone:** Practical. Suggest comfort-oriented options based on climate and activities, but respect each traveler's autonomy — don't over-prescribe.

### Companion App (Offline Web App)

Searchable digital itinerary for phone use. Single-page app bundling all trip data as embedded JSON, with PWA manifest for offline support.

## Production Workflow

1. **Plan first.** For non-trivial deliverables, write a brief plan in `memory/plans/` before building.
2. **Co-review content.** Review content with the user in plain text before wrapping it in formatting code. Adjusting content is much easier before it's in a generation script.
3. **Generate in one pass.** Once content is approved, write the generation script in full. Don't build incrementally — cumulative edits to generation scripts introduce bugs.
4. **Save to `outputs/`.** Every deliverable goes there with a descriptive name. Keep generation scripts alongside for reproducibility.
5. **Test the output.** Open/read the generated file to verify before presenting.

## Known Technical Pitfalls

These are common issues when generating PDFs programmatically (especially with ReportLab):

- **Table text overflow:** Plain strings in `Table` cells don't wrap. Always wrap cell content in `Paragraph` objects with a `ParagraphStyle`. Use `USABLE_WIDTH - col1_width` for the last column so tables fill to the margin.
- **Awkward page splits:** Tables and short sections can split across pages, leaving orphan rows. Wrap small self-contained sections in `KeepTogether()`. Don't use it on large tables that naturally span a full page.
- **Incremental editing is fragile:** When more than ~10 regex/sed edits are needed on a generation script, rewrite from scratch. Co-review content first, then generate the full script in one pass.
- **Bold-line rendering:** Consecutive lines starting with `**Bold:** value` (no blank line between) render as one paragraph in most markdown renderers. Always insert blank lines between bold-prefixed lines.
