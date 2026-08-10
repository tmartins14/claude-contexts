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
- footballd3's source lives in the football-analytics repo (`src/footballd3`), not
  here. This repo consumes it and is where the components get showcased.
- The StatsBomb JSON data is stored here (data/).

## Deploying — footballd3 is consumed from npm, not from the workspace
The single biggest source of broken deploys. Locally, `node_modules/footballd3` is a
**symlink** into football-analytics and the workspace root hoists dependencies. Vercel
clones this repo alone and installs from the npm registry, so it gets neither.

- **footballd3 must be published for the deploy to see a change.** A local edit is
  invisible to Vercel until `npm publish` runs and this repo's dependency is bumped.
  Publishing needs a real terminal — the npm account has 2FA set to `auth-and-writes`,
  so it fails with `EOTP` when run through a non-TTY like Claude Code.
- **`preserveSymlinks: true` in tsconfig.json is load-bearing.** Without it TypeScript
  follows the symlink outside `node_modules` and infers types from untyped JS, so the
  local build passes on errors a real install rejects. Don't remove it.
- **Every footballd3 export needs a `.d.ts`.** They're generated from JSDoc via
  `tsc --declaration --allowJs --emitDeclarationOnly`, not hand-written.
- **`make verify-deploy` before pushing** (WORKFLOW.MD 4d). A green `npm run build`
  is not evidence the deploy works; that check does a clean `npm ci` build of tracked
  files with no workspace parent.
- Commit the lockfile. Without one Vercel resolves dependencies unpinned every build.

## Current focus
- Candidate next steps overlap with football-analytics: (a) address tech debt,
  (b) build out the component library, or (c) a new view (e.g. Chelsea Vizathon
  player fitness management).
