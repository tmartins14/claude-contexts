# Yahoo Live Draft Assistant — Project Context

## What this is
A local, single-user live draft companion for Yahoo fantasy football snake drafts.
It syncs to a live draft, joins picks against my own rankings CSVs, and surfaces the
decisions that are hard to make fast under pressure: tier cliffs, pick-distance math,
positional runs, opponent prediction, and roster fit. **It advises; it never drafts.**

Intended to ship as a **public self-host repo** — a friend can clone it and run it with
their own credentials and their own rankings. Not a hosted service; per-user OAuth
custody and uptime are explicitly out of scope.

Full design lives in the repo at `docs/spec.md`; `CLAUDE.md` there is the operating guide.

## Stack
Python 3.13 · pandas · SQLite · FastAPI + uvicorn serving one polling HTML page ·
`yahoo_oauth` + `yahoo_fantasy_api` (not hand-rolled OAuth) · rapidfuzz for name
matching · pytest + ruff. FastAPI was chosen over Streamlit for draft-day reliability —
Streamlit's rerun model fights a background poller.

## Architecture — the load-bearing contracts
- **`PickSource` interface** with two implementations, `YahooLivePoller` and
  `ReplaySource`. All analytics run offline through `ReplaySource`; nothing downstream
  may import the live poller. This is what makes the engine testable without a draft.
- Flow: poller → diff → SQLite state store → pure analytics engine → `/state` JSON →
  polling web page. Poll cadence is decoupled from UI refresh, so a Yahoo hiccup serves
  the last known board instead of blanking the screen.
- **Per-league rankings profiles** on two axes: scoring variant (half/full PPR, RB/WR/TE
  only) × QB context (1QB vs superflex). Scoring variant is **derived from Yahoo league
  settings**, never hand-set. Profile A orders by ADP; Profile B orders by the superflex
  consensus rank and joins tiers/points from the position files.
- One active league per session (my drafts don't overlap). Crosswalk built once, shared.

## Yahoo API access is gated (found 2026-08-12)
Yahoo **retired the "Fantasy Sports" permission checkbox** in the developer console. The
API Permissions section is now empty for every app, and creating a new app does not help.
Access is granted per-application by a human review at
<https://sports.yahoo.com/developer/access/> — read-only; write access is not offered.

Until approved, OAuth succeeds and issues a perfectly valid token, but every fantasy
endpoint returns 401 `additional_authorization_required`. That error means "not approved
yet", not "bad credentials" — the tool translates it into the application link so the
failure isn't a mystery.

**Consequence for planning:** approval lead time is now the longest pole on live sync, and
it gates the crosswalk (needs Yahoo's player universe), the settings pull, and draft-day
serving. All offline work — rankings, profiles, analytics, replay harness — is unaffected
and was built without it.

## Hard rules
- **Never commit rankings CSVs.** They're paid member content; redistributing them in a
  public repo breaks the provider's terms. `data/` is gitignored; only a synthetic
  sample (fake players) is committed, for tests and first-run.
- **Never commit secrets or tokens.** Credentials live in a gitignored `.env`; each user
  registers their own Yahoo app. Ship `.env.example` only.
- **Nothing hardcoded to my leagues.** League keys, draft slot, team names come from
  config or runtime.
- **Rankings-agnostic.** Capability-detect by which files and columns are present;
  disable unsupported features with a clear message, never crash.

## Data facts worth remembering
- ADP is `round.pick` on a **fixed 12-team basis** and must be parsed as a string split
  on `.` — never as a float, or `"6.04"` and `"6.4"` collapse together.
- Roughly a quarter of skill players (78 of 312 in the current set) have **no ADP at
  all**. They are excluded from value and survivability math and labelled `no ADP`,
  never given an imputed number.
- The superflex file joins to the position files exactly on
  `(normalized name, position, team)`, and that key has no collisions in the data.
- The half-PPR and full-PPR files do **not** contain identical player sets, so the
  crosswalk is built over the union of all files, not one variant.

## Conventions specific to this project
- Function names are camelCase (global convention), unusual for Python; ruff's
  pep8-naming is deliberately not enabled. Modules and SQL identifiers stay snake_case.
- The analytics engine is pure — board state in, recommendations out, no I/O. That is
  what makes replay testing trivial.
- Validation errors on user data must name the file and column
  (`wr_ppr file missing column 'ADP'`) — I won't be there to debug a friend's setup.

## Modeling guardrails
- Manager tendencies (opponent modelling) are **gated on a retro test** showing lift over
  plain ADP. If it's flat, ship without them rather than build on noise.
- Draft data is time-ordered: never train tendency models on the draft they're evaluated
  against. Hold out, and flag any train/test overlap.
- Name matching is the highest-risk component. A silent mis-match leaves a drafted player
  on the board — the worst possible failure, because it's invisible and it's wrong at the
  moment it matters most.
