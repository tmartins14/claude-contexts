# tylermartins.com — Progress Log

Append-only, newest entry at top. One line per shipped feature/decision, not a
diff-by-diff changelog — see `tylermartins-progress.md`'s companion `tylermartins.md`
for current focus and `style.md` for design tokens.

## Log

2026-08-15 — Design remediation Ticket 1 (Foundations) merged: type/spacing/motion
token scales, hoisted layout/breakpoint constants, a real dashboard tablet tier
(768–1279px, was falling through to mobile tabs), and an ESLint rule that fails the
build on new arbitrary Tailwind values. `DESIGN.md` (new, in-repo) + `style.md` now
carry the full token reference, kept in sync going forward. Tickets 2–4
(color law, a11y/social, content model) queued next, same branch-per-ticket pattern.
