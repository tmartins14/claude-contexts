# tylermartins.com — Progress Log

Append-only, newest entry at top. One line per shipped feature/decision, not a
diff-by-diff changelog — see `tylermartins-progress.md`'s companion `tylermartins.md`
for current focus and `style.md` for design tokens.

## Log

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
