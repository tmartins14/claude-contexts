# Sleeper FPL Draft Assistant — Progress Log

Append-only. Newest at top. One line per meaningful ship or decision — not per commit.
Format: `YYYY-MM-DD — <what shipped / what was decided>`

## Log
- 2026-08-11 — First build shipped, spec milestones 0–5: Phase 0 API spike, player map
  resolving 150/150 rankings to Sleeper ids, live pick sync, roster/need tracking across
  the league's three distinct flex slots, recommendation engine, and a synthetic replay
  harness (the real draft is `pre_draft`, so there was no live feed to build against).
  The spike answered the blocking unknowns: sport string is `clubsoccer:epl`, and
  `/players/clubsoccer:epl` *does* work — which moved name matching from every poll to a
  one-off build step, the single biggest design change from the spec. Three findings
  reversed plan assumptions: the ETag optimisation is impossible from a browser (Sleeper's
  preflight rejects `If-None-Match`, silently breaking every poll after the first); the
  ranked pool can never run dry as a whole, so the endgame fallback became per-position;
  and the player dictionary spans 135 clubs worldwide with `competitions: ["epl"]` on every
  record, so EPL membership has to be inferred from the rankings. Decided Sleeper's club
  and position beat the CSV's on conflict, since `enforce_position_limits` means Sleeper's
  are what the draft enforces. Still outstanding: nothing is verified against real pick
  objects — `/picks` is still `[]` — so a mock-draft dry run before draft day is the one
  remaining gate.
