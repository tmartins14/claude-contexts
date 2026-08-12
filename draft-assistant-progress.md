# Yahoo Live Draft Assistant — Progress Log

Append-only. Newest at top. One line per meaningful ship or decision — not per commit.
Format: `YYYY-MM-DD — <what shipped / what was decided>`

## Log
- 2026-08-12 — Yahoo OAuth working end to end (two-step, no-TTY flow; token caches and
  auto-refreshes). Discovered Yahoo has **retired the Fantasy Sports permission checkbox**:
  API access is now a human-reviewed application at sports.yahoo.com/developer/access/.
  Live sync is blocked on that approval — a valid token still 401s with
  `additional_authorization_required` until granted. Translated that error into the
  application steps, and rewrote the README setup section around the new gate. Offline
  path (rankings, analytics, replay harness) is unaffected. Also hardened auth: split
  authorization into two non-interactive commands, and silenced yahoo_oauth's DEBUG
  logging, which was printing the client id into logs on every run.
- 2026-08-11 — First build pass shipped, spec milestones 0–5: repo scaffolded with data
  and secret hygiene from the first commit, Yahoo OAuth + settings pull with scoring
  variant and QB context derived (not configured), rankings loader/normalizer with
  capability detection, per-league profile binding, identity crosswalk with an
  unmatched-player report, `PickSource`/`ReplaySource`/state store, and the four core
  analytics (best-available, tier cliffs, pick-distance/survivability, roster needs)
  behind a FastAPI board. 141 tests green. Decided blank ADP is treated as *no signal*
  like K/DST rather than imputed — the source data doesn't carry a market number for
  those players and inventing one would quietly mislead. Verified against the real data:
  superflex profile puts Allen at SF#1 with 5 QBs in the top 11 and joins tiers 311/311.
  `rauth`/`yahoo_oauth` imports fine on Python 3.13, so no `requests-oauthlib` fallback
  was needed. Still outstanding: interactive first-run OAuth, the live crosswalk gate
  (needs Yahoo's player universe), forward-sim, and the throwaway-league rehearsal.
