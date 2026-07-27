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
- Nothing beyond the global CLAUDE.md yet. Add here as patterns emerge (footballd3
  component structure, view layout, JSON organization).

## Modeling guardrails
- Watch for silent overfitting. Always hold out a proper validation set.
- Flag any leakage between train/test on time-ordered data.

## Current focus
- Just shipped the match analysis dashboard.
- Next step undecided — choosing between: (a) addressing tech debt, (b) building out
  the component library on tylermartins.com, or (c) a new view (e.g. last year's
  Chelsea Vizathon on player fitness management).
