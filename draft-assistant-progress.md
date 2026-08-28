# Yahoo Live Draft Assistant — Progress Log

Append-only. Newest at top. One line per meaningful ship or decision — not per commit.
Format: `YYYY-MM-DD — <what shipped / what was decided>`

## Log
- 2026-08-28 — Shipped manual-entry draft mode (`draft-assistant draft`), since Yahoo
  approval won't land before the draft. A new `draft.yaml` (roster shape, scoring,
  QB context, league members in snake-slot order) substitutes for the Yahoo settings
  pull that mode can't use. Picks are typed in by hand via a web typeahead and default-
  assigned to whoever's on the clock (snake order), with an override for catching up
  out of order; undo covers mis-entries. No Yahoo code touched — player identity is
  just the existing (name, position, team) key, with a tiny crc32 id used only because
  the shared SQLite `Pick` schema wants an int. Also built the two analytics pieces the
  spec always wanted but hadn't shipped: an opponent forward-sim (full ~200-run Monte
  Carlo, needs + board-order proxy, no manager tendencies since those need Yahoo
  history) and a tier+need pick recommendation (bye/risk tiebreakers deliberately
  deferred). Rankings CSVs hot-reload mid-draft (poll-driven + a manual button) without
  losing drafted-player state, since that's keyed off the same stable player key.
  248 tests green, ruff clean; smoke-tested end-to-end in a live browser session
  (search → record → team-override → undo → rankings reload) against the synthetic
  sample, catching and fixing one real bug (`style.display=''` silently falling back
  to a CSS `display:none` rule instead of showing the typeahead results).
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
