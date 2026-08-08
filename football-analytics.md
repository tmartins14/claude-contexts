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
  demand it.
- Storage: flat JSON files today. Intended future move to a cloud database.

## Conventions specific to this project
- Documentation gate: `scripts/check_docs.sh` (100% Python docstrings via
  interrogate, JSDoc on every exported JS function, README.md per footballd3
  component dir). Wired into a Claude Code Stop hook. See `docs/tooling.md`.
- Test gate: `scripts/check_tests.sh` (pytest for `statsbomb`, vitest+jsdom for
  `footballd3`), wired into the same Stop hook alongside the docs gate. Python
  tests live in a mirrored `tests/unit`/`tests/integration` tree; JS tests are
  co-located as `<name>.test.js` next to each component. See `docs/testing.md`.

## Modeling guardrails
- Watch for silent overfitting. Always hold out a proper validation set.
- Flag any leakage between train/test on time-ordered data.

## Current focus
- Shipped the match analysis dashboard.
- Player Match Analysis (any non-GK player in the Euro 2024 Final, selectable)
  is fully built end-to-end, including a first browser-review fix pass (SSR
  crash, heatmap orientation bug, goal-net visual, highlight reel styling,
  a 900-1023px breakpoint overlap). All gates green (pytest, vitest, docs,
  eslint, tsc, e2e). Both repos committed locally on a `player-match-analysis`
  branch, neither pushed — next step is your review, then push + PR.
