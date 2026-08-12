# Sleeper FPL Draft Assistant — Progress Log

Append-only. Newest at top. One line per meaningful ship or decision — not per commit.
Format: `YYYY-MM-DD — <what shipped / what was decided>`

## Log
- 2026-08-12 — Recommendation engine v2 after a live mock draft showed v1 was effectively
  "take the highest-ranked player". Rebuilt around opportunity cost — value minus the
  expected best survivor at that position — fed by an opponent model that runs
  `computeNeeds` over every rival roster and blends it with the observed positional run
  rate. Added the four-mark scale (must/star/flag/never) with the specified windows, and
  deferred GK to the last pick (the mock ended with no keeper, which is exactly what it
  prevents). Three modelling bugs found and fixed while building: linear value
  normalisation let need outvote talent at pick 1; within-position decay weights summed to
  1.8 rather than 1, inflating every survival probability by 80%; and the "2 tiers above
  the next player" escape compared against `available[0]`, which is that player, so the
  rule could never fire. Verified with a v1-vs-v2 harness: they diverge on 6 of 17 picks
  and 5 of those sit inside the flat tiers, which is where the gain was supposed to be.
  77 self-test checks.

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
