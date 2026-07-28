# tylermartins.com — Project Context

## What this is
My personal portfolio site. Football analytics is one section; other non-football
sections get added here as I build them. Audience: showcase for roles (e.g.
Arsenal-type research/engineering) and general portfolio.

## Stack
- Next.js — a custom/breaking version. Per AGENTS.md, read the relevant guide in
  node_modules/next/dist/docs/ before writing Next code; don't assume training-data APIs.
- TypeScript, Tailwind, shadcn/ui components.
- Playwright for e2e.
- Part of the `my-portfolio` npm-workspace monorepo. Workspaces: footballd3
  (football-analytics/src/footballd3) and this site.

## Where things live
- This repo is where the footballd3 component library components get built.
- The StatsBomb JSON data is stored here (data/).

## Current focus
- Candidate next steps overlap with football-analytics: (a) address tech debt,
  (b) build out the component library, or (c) a new view (e.g. Chelsea Vizathon
  player fitness management).
