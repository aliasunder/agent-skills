# Evals

## Overview

Each eval tests whether the skill produces better trip planning outputs than a baseline agent without the skill. The same prompt and mock project files are given to two agents — one with SKILL.md loaded ("with_skill") and one without ("without_skill") — and both outputs are graded against the same assertions.

## How evals are run

There is no automated test harness. Evals are run manually using Claude agents (subagents in Cowork or Claude Code — both support plugins) in a **with-skill vs without-skill** comparison pattern:

### Running a single eval

1. **Set up mock files.** Each eval in `evals.json` has a `files` array — these are the pre-seeded project files that simulate a specific planning state. Create them in a temporary directory.

2. **Run the "with_skill" variant.** Spawn an agent and tell it to:
   - Read `SKILL.md` and any relevant reference files
   - Act on the eval's `prompt` as if it came from the user
   - Work within the mock project directory
   - Create/modify files as the skill instructs

3. **Run the "without_skill" variant.** Spawn a separate agent with the same prompt and mock files, but explicitly instruct it NOT to read any skill files. This is the baseline — a capable general-purpose agent without specialized trip planning instructions.

4. **Grade both variants.** Spawn a grading agent for each. Give it:
   - The assertion list from `evals.json`
   - The path to the output files
   - Instructions to check each assertion and cite specific evidence (quotes, line numbers, file existence)

5. **Compare.** The delta between with-skill and without-skill scores shows the skill's value-add for that scenario.

### What this means in practice

Each eval run involves 4+ agent invocations (2 test agents + 2 grading agents) and takes 5-15 minutes total. Results are stored in `iteration-N/` directories with `benchmark.json` summarizing scores. This is a qualitative evaluation process, not a CI pipeline — the grading agents use judgment, not regex matching, so there's inherent variance between runs.

### Could this be automated?

Partially. The eval definitions in `evals.json` are structured enough to drive a programmatic runner (create temp dir → seed files → spawn agent → collect outputs → grade). The skill-creator skill in the plugin ecosystem has eval infrastructure that could be adapted. The main barrier to full automation is grading — assertions like "research is visibly shaped by booking philosophy" require semantic evaluation, not string matching. A hybrid approach (automated runner + LLM grader) would work but hasn't been built yet.

## Test scenarios

Defined in `evals.json`. There are 13 scenarios covering distinct phases and behaviors:

### Eval 1: `new-trip-scaffolding`
**Phase tested:** Phase 1 (Define) — starting from nothing.
**Setup:** Empty workspace, no pre-existing files.
**Prompt:** User describes a 10-day Japan trip with partner Alex (bad knee, language anxiety, vegetarian + omnivore, $8K budget, 85K United miles).
**Tests:** Does the agent create the right file structure, put the right information in the right files, and avoid common pitfalls like dumping everything into CLAUDE.md?

### Eval 2: `mid-project-session-start-and-research`
**Phase tested:** Session continuity + Phase 6 (Restaurants).
**Setup:** Pre-seeded mock files simulating a mid-project state — CLAUDE.md (Phase 5/6), TASKS.md (active/done tasks), last session log. See the `files` array in evals.json for the exact mock content.
**Prompt:** User asks to check status and start Kyoto restaurant research.
**Tests:** Does the agent follow session-start protocol, create a research file (not a guide), present recommendations in day-by-day context, and avoid premature guide creation?

### Eval 3: `session-end-task-reconciliation`
**Phase tested:** Session close — the hardest skill behavior.
**Setup:** Pre-seeded mock files with unchecked restaurant subtasks in Active, a ryokan confirmation pending in Waiting On, and an open question about the ryokan in CLAUDE.md.
**Prompt:** User says they're done — finished Kyoto + Tokyo restaurants (approved), and the ryokan confirmed (ref KYO-8842).
**Tests:** Does the agent physically move tasks to Done (not just check them), close resolved waiting items, update open questions, and correctly leave the parent task open when one subtask (Osaka) is still pending?

### Eval 4: `premature-phase-jump`
**Phase tested:** Phase enforcement — user tries to skip ahead.
**Setup:** Empty workspace, no pre-existing files.
**Prompt:** User wants to research Kyoto hotels but hasn't defined budget, traveler profiles, route, or booking philosophy.
**Tests:** Does the agent redirect to prerequisites rather than diving into hotel research? Does it explain *why* the missing context matters (not just cite phase rules)?

### Eval 5: `booking-philosophy-shapes-research`
**Phase tested:** Phase 4 (Accommodation) — whether documented preferences actually drive research.
**Setup:** Mid-project with CLAUDE.md (booking philosophy, star preference, traveler profiles), traveler profiles in memory/people/, last session log.
**Prompt:** User asks to research Kyoto hotels (4 nights).
**Tests:** Does the research reflect the booking philosophy (refundable, comfort premium, 3-4 star)? Are traveler constraints (Alex's knee → elevator, Jordan's diet → vegetarian breakfast) used as filters? Does it include above-range properties when price-competitive (per updated skill guidance)?

### Eval 6: `post-approval-guide-creation`
**Phase tested:** Research → guide pipeline — the handoff after user approves recommendations.
**Setup:** Pre-seeded research file with 6 shortlisted restaurants and day-by-day recommendations. User is approving picks.
**Prompt:** User approves Kyoto restaurant picks and asks to "finalize everything."
**Tests:** Does the agent create a curated guide (not a copy of research)? Does it preserve the research file (coexistence principle)? Does it update TASKS.md and CLAUDE.md?

### Eval 7: `mid-planning-pdf-material`
**Phase tested:** Decision-support materials — PDF creation mid-planning (not Phase 7).
**Setup:** Full project state with itinerary, hotel bookings, activity guides for all cities. Restaurants not yet started.
**Prompt:** User wants a warm PDF overview of the trip to share with travel companion before picking restaurants together.
**Tests:** Does the agent read `references/materials-guide.md` before building? Does it co-review content structure before generating? Does it use real trip data (not generic content)? Does it stay within the trip-planner skill context (not defer to a separate PDF skill)?

### Eval 8: `materials-guide-read-before-build`
**Phase tested:** Phase 7 (Pre-Trip Prep) — specifically tests the "read reference before acting" discipline.
**Setup:** Complete project state with all bookings, activities, and restaurants decided. Phase 7 with daily cards as the first material to produce.
**Prompt:** User asks for printed daily cards — one page per day, B&W, with schedule/transport/restaurants/emergency info.
**Tests:** Does the agent read `references/materials-guide.md` *before* writing any code? Does it follow the daily card specification from the guide? Does it co-review content before generating? Does it apply the documented ReportLab pitfalls? This eval specifically targets the strengthened reference-read prompting added after iteration 4 showed agents skipping the reference file.

### Eval 9: `quick-query-train-time`
**Phase tested:** Quick query — tests whether the skill adds unnecessary overhead on simple factual lookups.
**Setup:** Full project state (real trip files).
**Prompt:** User asks what time their first train departs on May 30.
**Tests:** Does the agent answer directly without a full session-start protocol? Does it stay under 5 tool calls? Does it avoid reading TASKS.md for a simple factual question?

### Eval 10: `quick-query-hotel-address`
**Phase tested:** Quick query — hotel lookup.
**Setup:** Full project state.
**Prompt:** User asks for their Verona hotel name and address.
**Tests:** Same overhead checks as eval 9 — direct answer, no session bloat, minimal tool calls.

### Eval 11: `quick-query-budget-buffer`
**Phase tested:** Quick query — budget calculation.
**Setup:** Full project state.
**Prompt:** User asks how much budget buffer they have left.
**Tests:** Does the agent pull correct figures? Does it avoid session-start overhead? Tool call threshold (3 or fewer — found to be too tight in practice).

### Eval 12: `quick-query-mobility`
**Phase tested:** Quick query — traveler-specific advice.
**Setup:** Full project state with traveler profiles.
**Prompt:** User asks whether mom can handle cobblestones in Avignon.
**Tests:** Does the agent reference mom's health details from her profile? Does it give practical, trip-specific terrain advice? Does it avoid session-start overhead?

### Eval 13: `planning-task-breakfast-research`
**Phase tested:** Mid-session research — tests that the skill properly activates for real planning work (vs. quick queries).
**Setup:** Full project state.
**Prompt:** User asks for Avignon breakfast spot recommendations.
**Tests:** Does the agent read traveler profiles and existing restaurant research? Does it present multiple options addressing both dietary needs? Does it include practical details (addresses, prices, proximity)?

## Assertions

Each eval has **positive** and **negative** assertions:

- **Positive assertions** check that something *should* happen (e.g., "session log written", "booking philosophy flagged"). Passes when the expected behavior is present.
- **Negative assertions** check that something should *not* happen (e.g., "no methodology in CLAUDE.md", "no premature guide creation"). Passes when the bad behavior is absent.

The full assertion list for each eval is in `evals.json` under `assertions.positive` and `assertions.negative`.

## Directory structure

```
evals/
  evals.json                        # Test definitions: prompts, mock files, assertions
  README.md                         # This file
  iteration-1/                      # First eval run
    benchmark.json                  # Aggregate scores
    new-trip-scaffolding/
      with_skill/
        grading.json                # Per-assertion pass/fail with evidence
        outputs/                    # Files the test agent created
          CLAUDE.md
          TASKS.md
          ...
      without_skill/
        grading.json
        outputs/
          ...
    mid-project-session-start-and-research/
      ...
    session-end-task-reconciliation/
      ...
  iteration-2/                      # After skill fixes (file boundaries, naming)
    ...
  iteration-2b/                     # Targeted re-test of eval 1 only
    ...
  iteration-3/                      # Evals 4-6 (phase enforcement, booking philosophy, guide creation)
  iteration-4/                      # Star-preference, research preservation fixes
  iteration-5/                      # Quick query overhead evals (9-13)
  iteration-6/                      # SKILL.md scaffolding improvements (agent working memory, when-to-read)
```

## How to read the results

### grading.json

Each run produces a `grading.json` with this structure:

```json
{
  "eval_id": 1,
  "eval_name": "new-trip-scaffolding",
  "configuration": "with_skill",
  "expectations": [
    {
      "text": "assertion-name",
      "passed": true,
      "evidence": "Specific quotes or line numbers from the output files"
    }
  ],
  "summary": {
    "passed": 10,
    "failed": 1,
    "total": 11,
    "pass_rate": 0.91
  }
}
```

The `evidence` field is the most useful part — it cites specific lines or quotes from the output files to justify the pass/fail decision.

### benchmark.json

Aggregates all runs in an iteration into a summary:

```json
{
  "run_summary": {
    "with_skill": { "pass_rate": { "mean": 0.94, "stddev": 0.09 } },
    "without_skill": { "pass_rate": { "mean": 0.76, "stddev": 0.17 } },
    "delta": { "pass_rate": "+0.18" }
  }
}
```

### outputs/

The actual files each test agent created. For eval 1, these are the full project scaffolding (CLAUDE.md, TASKS.md, traveler profiles, etc.). For evals 2-3, these are the updated files after the agent did its work.

Compare `with_skill/outputs/` vs `without_skill/outputs/` to see the qualitative difference the skill makes. The most revealing comparisons:

- **CLAUDE.md size and content** — with-skill should be lean (~80-200 lines, pure project state). Without-skill tends to dump methodology, session protocols, and full traveler details inline.
- **Task reconciliation** (eval 3) — with-skill physically moves completed tasks to Done. Without-skill often just checks them off in Active.
- **File naming** — with-skill uses `restaurant-kyoto.md`. Without-skill sometimes creates `research-restaurant-research-kyoto.md`.

## How grading works

Grading is done by a separate Claude agent that reads all output files and evaluates each assertion. The grading agent:

1. Reads every file in the `outputs/` directory
2. For each assertion, searches for evidence (specific lines, sections, file existence)
3. For positive assertions: passes if the expected behavior is found
4. For negative assertions: passes if the bad behavior is *absent*
5. Writes evidence quotes to justify each decision

The grading agent is strict — it cites line numbers and quotes. This makes it possible to audit the grades by reading the evidence and cross-referencing the output files.

## Iteration history

| Iteration | What changed | Evals run | With-skill | Without-skill | Delta |
|-----------|-------------|-----------|-----------|--------------|-------|
| 1 | Initial run, positive assertions only | 1-3 | 94% | 67% | +27% |
| 2 | Added negative assertions, file boundary rules, naming fix | 1-3 | 94% | 76% | +18% |
| 2b | Strengthened booking philosophy + people table guidance | 1 only | 91% | — | — |
| 3 | Added evals 4-6 (phase enforcement, booking philosophy, guide creation) | 4-6 | Mixed | Mixed | Identified star-preference and research-file-preservation gaps |
| 4 | Star-preference nuance, research file preservation, MCP tools section | 5-7 | 96% (22/23) | 75% (6/8, eval 7 only) | +21% on eval 7; evals 5+6 improved to 100% |
| 5 | Quick query overhead evals (9-13) — does the skill cause bloat on simple lookups? | 9-13 | 95% | 95% | +0% — skill does NOT add overhead on quick queries |
| 6 | SKILL.md scaffolding: agent working memory label, "When to read" triggers, "start here each session" | 1-2 | 92.3% (12/13) | 75% (9/12) | +17.3pp on scaffolding; session-start 100% both |

**Key findings across iterations:**
- The skill's biggest value-add is in research methodology and file management disciplines (evals 1-3, 5-6).
- For material production (eval 7), the skill provides moderate advantage (+1 point) — mainly through the co-review workflow.
- Iteration 3→4 showed that targeted SKILL.md fixes (star preference, research preservation) directly improved eval scores, validating the iterate-and-test loop.
- Iteration 5 confirmed the skill does NOT cause unnecessary overhead on simple factual lookups — both configurations perform equally on quick queries. The skill adds ~5s and ~7.5K tokens on average from loading SKILL.md.
- Iteration 6 showed that scaffolding instructions (agent working memory label, file map with "When to read" triggers) produce meaningfully better CLAUDE.md structure. Persistent issue: both versions create meta-files (INDEX.md, etc.) despite explicit prohibition.
