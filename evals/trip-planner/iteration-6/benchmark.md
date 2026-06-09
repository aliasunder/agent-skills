# Iteration 6 — SKILL.md scaffolding improvements

**Date:** 2026-04-02
**Changes tested:** Added "agent working memory" label, "When to read" column with bold triggers, "start here each session" for TASKS.md, "no meta-files" instruction.

## Results

| Eval | with_skill | old_skill | Delta |
|------|-----------|-----------|-------|
| new-trip-scaffolding | 92.3% (12/13) | 75.0% (9/12) | +17.3pp |
| mid-project-session-start | 100% (8/8) | 100% (8/8) | — |

## Scaffolding improvements (Eval 1)

New assertions added this iteration:
- **agent-working-memory-label:** ✅ with_skill, ✅ old_skill (both pass — old skill happened to produce it too)
- **file-map-has-when-to-read:** ✅ with_skill, ❌ old_skill (key win — old skill had column header but no contextual triggers)
- **tasks-md-labeled-as-tracker:** ✅ with_skill, ✅ old_skill (both pass)

Persistent failure (both versions):
- **no-meta-files:** ❌ both — with_skill creates INDEX.md/PROJECT_STRUCTURE.md/etc., old_skill creates README.md/FILE_MANIFEST.md/etc.

Old_skill additional failures:
- **no-inline-traveler-details:** ❌ (inlined Alex's knee and anxiety details in CLAUDE.md People table)
- **file-map-has-when-to-read:** ❌ (generic labels like "Your traveler profile" instead of contextual triggers)

## Session-start (Eval 2)

No regression — both versions achieve 100%. Session-start protocol, research methodology, and guide separation all work correctly.

## Timing

| Eval | with_skill | old_skill |
|------|-----------|-----------|
| new-trip-scaffolding | 232.0s / 84.8K tokens | 288.8s / 86.2K tokens |
| mid-project-session-start | 251.1s / 79.0K tokens | 292.6s / 87.3K tokens |

with_skill is faster on both evals (~20% time reduction).

## Known issues

- Meta-file creation persists despite explicit prohibition. May need stronger negative instruction or post-scaffolding cleanup step.
