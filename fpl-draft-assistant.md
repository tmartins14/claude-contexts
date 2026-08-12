# Sleeper FPL Draft Assistant — Project Context

## What this is
A live draft-day companion for an 8-team **Sleeper Fantasy Premier League** snake draft.
It polls the real draft over Sleeper's public API, tracks picks and my roster, and
recommends who to take next from positional need plus who probably won't survive to my
next pick. Read-only against Sleeper — it advises, it never drafts.

Single-user, single-session, laptop-only. Not the same project as the Yahoo NFL draft
assistant (see [draft-assistant.md](draft-assistant.md)) — different sport, different
provider, different stack, no shared code.

Spec lives in the repo at `sleeper-draft-assistant-spec.md`; API findings in
`SPIKE-FINDINGS.md`.

## Stack
Vanilla ES modules, no build step, no npm install — a static page served by
`python3 -m http.server`. Deliberate: nothing to break on draft day. JSDoc `@typedef` on
module boundaries stands in for type hints. Node is used only for the two offline tools
(`buildPlayerMap.mjs`, `replay.mjs`).

No backend. Sleeper sends `access-control-allow-origin: *`, so the browser calls the API
directly; the thin proxy the spec allowed for is dead scope.

## Sleeper API facts — none of this is documented
Sleeper's public docs cover `sport=nfl` only. Everything below was measured.

- **The sport string is `clubsoccer:epl`** — colon-namespaced. Not guessable; `epl`,
  `soccer`, `fc`, `football`, `fpl` all return empty. A `null` from
  `/state/<sport>` proves nothing: that endpoint only knows nfl/nba/lcs/mlb.
- **`/players/clubsoccer:epl` works** — 2.19 MB, 1817 players with names, clubs,
  positions. So picks resolve by `player_id`; no fuzzy matching during a live draft.
- **The dictionary is not EPL-only.** It spans 135 clubs worldwide, and *every* record
  carries `competitions: ["epl", …]`, so that field can't filter. Squad size doesn't help
  either — the biggest squads include relegated clubs. League membership has to be
  inferred from the clubs present in the rankings, widened by clubs seen in real picks.
- **`If-None-Match` is unusable from a browser.** The `etag` is readable (it's in
  `access-control-expose-headers`), but sending it promotes the GET to a preflighted
  request and Sleeper answers `OPTIONS` with 204 and *no* `access-control-allow-headers`,
  so the browser blocks it and `fetch` rejects. ETags can only be compared client-side to
  skip a re-render. This broke every poll after the first until it was found.
- **`search_rank`** (1041/1817 populated) is Sleeper's own popularity ordering — Haaland 1,
  Palmer 2. Weakly correlated with consensus rank (r ≈ 0.23), but the only sane way to
  order players the rankings don't cover.

## Data facts worth remembering
- The consensus CSV packs rank and name in one column (`"1: Bruno Fernandes"`), carries a
  UTF-8 BOM, and is already ASCII-folded (`Estevao`) while Sleeper is not (`Estêvão`).
- **`200` in an analyst column means "unranked by that analyst", not a rank of 200.** 130
  of 1350 cells. Treating it as a rank makes every partially-ranked player look maximally
  controversial in the variance signal.
- **Sleeper's own `search_full_name` is lossy** — it renders `Groß` as `gro` and
  `Ødegaard` as `degaard`, dropping characters NFKD won't decompose. Normalise `full_name`
  yourself with an explicit fold map.
- Club abbreviations disagree between sources (`NOT`/`NFO`, `BRF`/`BRE`), and players
  transfer, so **club is a matching tiebreaker only, never a key**. Sleeper's club wins for
  display — it's live, the CSV isn't.
- **Sleeper's position wins over the CSV's** (they disagree on 10 of 150), because
  `enforce_position_limits` means Sleeper's is what the draft actually enforces.
- **The ranked pool never runs dry as a whole**: 150 ranked against 136 total picks, so
  ≥14 always survive, and every unranked pick leaves *more* ranked players standing. Only
  per-position exhaustion is real.
- **No goalkeepers in the rankings at all**, but the league starts one. That slot is
  unranked by construction.

## Conventions specific to this project
- Every tunable lives in `js/config.js` — the engine is a heuristic that gets tuned live,
  so there is exactly one file to open mid-draft.
- Variance is a *qualifier* on risk, never folded into the score: "high risk, analysts
  agree" and "high risk, high variance" are different calls.
- Never silently drop a pick that fails to resolve — an unmatched pick leaves a drafted
  player showing as available, which is wrong exactly when it matters. Surface it.
- Never invent a rank. Where there's no data (GK, endgame fallback) the UI says so.

## Draft-day operational notes
- **Connect retries 4 times before complaining**, and only for network-layer failures — a
  404 on the league id fails identically every time, so it reports at once. A transient
  blip that would once have dumped you to an error banner now self-heals silently.
  Observed at least one real transient failure against Sleeper that was not reproducible
  a minute later, so this is not theoretical.
- `fetch` rejects with a bare "Failed to fetch" for offline, DNS, blocked-by-extension and
  CORS alike. Every error surfaced to the UI names the endpoint and what to check —
  during a draft there is no time to open devtools.
- **Keep the tab visible.** Chrome throttles hidden tabs — measured at ~895 ms per tick
  for an empty timer chain — so a backgrounded tab polls about once a minute instead of
  every 6 s, and Chrome eventually freezes it outright and fetches start failing. The app
  re-polls on `visibilitychange`, but that's a recovery, not a substitute.
- `draft_order` is `null` until the commissioner sets it, and `slot_to_roster_id` is a
  placeholder identity map before that. Draft slot is genuinely unknowable pre-draft, so
  survival risk stays unavailable rather than guessing.
