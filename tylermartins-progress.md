# tylermartins.com — Progress Log

Append-only, newest entry at top. One line per shipped feature/decision, not a
diff-by-diff changelog — see `tylermartins-progress.md`'s companion `tylermartins.md`
for current focus and `style.md` for design tokens.

## Log
- 2026-08-18 — Fixed a real regression reported live ("responsive design is
  broken") on PR #18: `AsyncSkeleton` (Ticket 4b's loading placeholder)
  shipped with fixed `grid-cols-3`/`grid-cols-2`, no responsive breakpoints —
  squished multi-column on a phone-width popup during the loading flash while
  the real content correctly collapsed to one column. Fixed by matching the
  same `pma-sm`/`pma-md` tokens the real content uses; new permanent test
  asserts `gridTemplateColumns` at 390px, proven via revert-and-retest. Only
  the transient loading state was ever affected, not the final loaded page.
- 2026-08-15 — Ticket 4 (Content model, async states, copy) PR #18 opened, not
  yet merged: `concept{}` field on the component schema (no render surface,
  deliberately deferred), real git-derived `publishedDate` backfill for all 16
  components (was one flat placeholder, made the gallery sort a no-op), a
  shared async state kit (skeleton/error/empty) with a new distinct empty
  state for near-zero-event players, and a tightened homepage H1 (Tyler's pick
  from the bundle's options: "I turn new football-analytics concepts into
  tools you can use."). All 4 tickets from the original remediation plan are
  now in PR — #16 (Ticket 3), #17 (hydration fix), #18 (Ticket 4) — pending
  merge, plus #(bump/footballd3-0.1.4) blocked on an npm publish only Tyler can
  do (see football-analytics-progress.md).

2026-08-15 — Fix PR #17 opened (unrelated to Ticket 3, reported live in dev by user):
a real hydration mismatch for dark-theme visitors — `TeamColumnCard` and
`MomentumBarPanel` read next-themes' `resolvedTheme` straight into JSX instead of
gating it behind a `mounted` check, so SSR (always "light") disagreed with a real
dark-theme visitor's first client render. Same bug already fixed once before in
`PlayerMatchAnalysisClient.tsx` — applied the same established `mounted`-guard pattern.
Audited every other chart panel's `useTheme()` usage; only these two had the
unguarded-in-JSX pattern. New permanent regression test in
`e2e/dashboard-responsive.spec.ts`, proven to actually catch it via revert-and-retest.

2026-08-15 — Ticket 3 (Accessibility & social metadata) PR #16 opened, not yet merged:
contrast-fixed `--faint`, sitewide `:focus-visible` rings, ARIA roles/labels across every
D3 chart panel, a shared OG image + real favicon/apple-icon, and OG/Twitter metadata on
every route. Two real bugs caught during verification (not just asserted "done"): Satori
mixing serif/bold glyphs in the OG image because "sans-serif" was never a registered
font name, and `og:image` silently missing on nested routes because Next.js metadata
merge is shallow and doesn't cascade file-convention images down the segment tree —
both confirmed against real rendered output and the vendored Next docs. One known,
flagged-not-fixed gap: gallery modal doesn't trap focus. Ticket 4 (content model,
async states, copy) next once this merges.

2026-08-15 — Ticket 2 (Data-ink & color law) merged, plus 3 post-launch fixes: dashboard
match score sizing (a dedicated `text-score` token, `display-1`'s clamp was reading
oversized in the compact header), the new tablet tier stretching cards full-width while
their pitches stayed desktop-scale-capped inside (real bug — width-capped + 2-column
above 900px now), and a `cn()`/`tailwind-merge` bug silently dropping every custom
type-ramp class combined with a color class in the same call (fixed at the root in
`lib/utils.ts`, was rendering buttons at 16px instead of 11px sitewide). All three
caught from real user reports, diagnosed with actual measurements/screenshots before
fixing, each with a permanent regression test. Tickets 3–4 (a11y/social, content model)
next.

2026-08-15 — Design remediation Ticket 1 (Foundations) merged: type/spacing/motion
token scales, hoisted layout/breakpoint constants, a real dashboard tablet tier
(768–1279px, was falling through to mobile tabs), and an ESLint rule that fails the
build on new arbitrary Tailwind values. `DESIGN.md` (new, in-repo) + `style.md` now
carry the full token reference, kept in sync going forward. Tickets 2–4
(color law, a11y/social, content model) queued next, same branch-per-ticket pattern.
