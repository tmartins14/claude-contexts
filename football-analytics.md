# Football Analytics — Project Context

## What this is
A football analytics playground focused on interactivity, tooling, and animation.
The goal is presentation, not novel analysis — take known analytical approaches and
present them in my style. Reference points: The Pudding, the Chelsea Vizathon, and
the kind of work in Arsenal's Research Engineer role. This is a portfolio/showcase
piece, hosted on tylermartins.com.

## Stack & data
- Analysis: Python
- Viz/animation: footballd3, a custom D3 component library (its own package, currently
  living inside this repo).
- Hosting: tylermartins.com
- Data: StatsBomb free open data for now; will branch out to other sources as new views
  demand it. `socceraction` added as a dependency (xT grid fitting).
- Storage: flat JSON files, committed to the repo for `data/euro-2024/` specifically
  (derived extractor output only — raw/processed data stays gitignored per
  `data/README.md`). Revisited 2026-10-17: at ~40MB, derived, reproducible from
  StatsBomb's public API, and read only by local scripts, this is fine as-is —
  not moving to a cloud database unless it grows toward the low hundreds of MB
  or a live multi-match consumer (e.g. tylermartins.com showing all 51 matches,
  not just the one hand-copied demo) actually shows up.

## Conventions specific to this project
- Documentation gate: `scripts/check_docs.sh` (100% Python docstrings via
  interrogate, JSDoc on every exported JS function, README.md per footballd3
  component dir). Wired into a Claude Code Stop hook. See `docs/tooling.md`.
- Test gate: `scripts/check_tests.sh` (pytest for `statsbomb`, vitest+jsdom for
  `footballd3`), wired into the same Stop hook alongside the docs gate. Python
  tests live in a mirrored `tests/unit`/`tests/integration` tree; JS tests are
  co-located as `<name>.test.js` next to each component. See `docs/testing.md`.

## AI-engineering work
- Lives under `ai/` — one self-contained subdirectory per project (code,
  SPEC/DATA-SPEC/VERIFICATION docs, and any generated output co-located).
  Consumes `libs/statsbomb` and `libs/footballd3` like `apps/` does, never
  the reverse. `apps/` is reserved for non-AI deployables.
- First project: `ai/match_summary/` — auto-written match summaries
  (structured outcome + free-prose tactics), Module 1 of an "AI Engineering
  Learning Vault" (Module 2: pressing/transition/set-piece data extractors;
  Module 3: eval; Module 6: RAG/agent cross-match Q&A — later modules land
  as their own `ai/<project>/` subdirectories as they're built).

## Modeling guardrails
- Watch for silent overfitting. Always hold out a proper validation set.
- Flag any leakage between train/test on time-ordered data.

## Current focus
- DATA-SPEC-v3 (phase-based tactical findings + player contribution rating,
  extending the match-summary tactical findings layer, closing failure
  classes D10/D11). Parts 0-2 shipped (PRs #22-26 merged, #27 open for
  review): data audit, `socceraction` dependency, four new tier-1 extractors
  (chance creation, progression, pressing, defending — raw values only),
  a full backfill of all 51 Euro 2024 matches, and both reference-data files
  (`reference_distributions.json` tercile cut points, `xt_grid_v1.json`).
  Part 3 (player contribution: the xT-grid-based rating, replacing
  model-chosen "key performers") is next. See
  `docs/task-history/2026-10-data-spec-v3-phase-findings.md` in the repo for
  full detail.
- Player Match Analysis (any non-GK player in the Euro 2024 Final, selectable)
  shipped earlier — fully built end-to-end, all gates green.
