# Known Issues

Pitfalls encountered in real trip planning projects. Read when creating memory files or debugging unexpected behavior in companion tools.

## Productivity Dashboard — escapeHtml() Bug

The productivity dashboard's `escapeHtml()` function doesn't escape double quotes. When it builds `data-search` HTML attributes from file metadata via `JSON.stringify`, unescaped quotes prematurely close the attribute. Any remaining file content spills out and gets parsed as raw HTML attribute tokens by the browser.

**The practical impact:** if a memory file contains HTML boolean attribute names as bare words — `hidden`, `disabled`, `readonly`, `required`, `checked` — the browser interprets them as actual HTML attributes on the dashboard card element. The word `hidden` is especially dangerous because it applies `display: none`, making the card silently disappear from the dashboard.

**Example:** A destination guide containing "Cannaregio's hidden gem" caused its dashboard card to vanish entirely. The badge counted the file, but it was invisible.

**Workaround:** When writing memory files (research, guides, session logs, profiles), avoid these words as bare text. Use synonyms instead:

| Avoid | Use instead |
|-------|-------------|
| hidden gem | quiet treasure, tucked-away spot, under-the-radar find |
| hidden | tucked away, out of sight, not obvious |
| disabled | unavailable, turned off, not accessible |
| readonly | view-only, not editable |
| required | mandatory, necessary, must-have |

This only matters when the productivity dashboard is in use alongside the trip planning project. The underlying fix would be for the dashboard to escape `"` to `&quot;` in `escapeHtml()`.

**Discovered:** Mar 14, 2026. See `memory/sessions/session-log-mar14-c.md` for full debugging story.
