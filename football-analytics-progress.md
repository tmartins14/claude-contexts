# Football Analytics — Progress Log

Append-only. Newest at top. One line per meaningful ship or decision — not per commit.
Format: `YYYY-MM-DD — <what shipped / what was decided>`

## Log
- 2026-08-05 — Built a test suite (pytest for statsbomb, vitest+jsdom for footballd3) and a matching scripts/check_tests.sh gate, wired into the same Stop hook as the docs gate. Caught a real bug along the way: pandas silently turns a None into NaN even in string columns, not just numeric ones — fixed in a new shared utils.clean_nan().
- 2026-08-05 — Built the Player Match Analysis Python extractors (extract_substitutes.py, extract_player_events.py) and footballd3 component extensions/new components (formation.js click-select, convexHull.js points-mode, scrubber.js, cumulativeXtChart.js) on branch player-match-analysis — any non-GK player selectable, not one hardcoded player. tylermartins.com wiring deferred to a future Claude Design → Claude Code handoff.
- 2026-07-29 — Set up shared-context system (scope, style, this progress log) in claude-contexts.
- 2026-07-29 — Shipped match analysis dashboard (desktop + mobile layouts) for a single match.
