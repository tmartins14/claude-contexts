# Design & Presentation Style — Shared

The look for tylermartins.com and the football-analytics viz. Source of truth for
tokens is `tylermartins.com/app/globals.css` (Tailwind v4, CSS-first). This file is
the human-readable intent — when they diverge, globals.css wins for values.

**Kept in sync with `tylermartins.com/DESIGN.md`** (in-repo, detailed token/rule
reference, visible without this private repo). Whichever changes, update the other in
the same edit — this file stays the terse version, DESIGN.md carries the full detail
(type ramp, spacing scale, motion tokens, kit table, a11y rules, content model).

## Archetypes
Two page types drive density + motion, not separate knobs:
- **Tool** pages (dashboards, FootballD3 gallery): dense, functional motion only,
  chrome recedes.
- **Piece** pages (editorial concept surfaces): essay-scaffolded, editorial motion
  allowed. Not built yet — don't create, just don't block it.

## Character
Warm editorial / data-journalism. Reference feel: The Pudding, Chelsea Vizathon.
- Paper, not screen: warm cream backgrounds (#FAF7F0), never stark white.
- Serif display headlines carry the editorial voice; body stays clean and neutral.
- Restrained palette: ONE focal accent (crimson) + ONE secondary (navy). Don't
  introduce new accent colors per view — encode data within these + neutrals.
- Pitch is drawn in the navy secondary, not green. Keep it.

## Color (semantic — use tokens, never raw hex in components)
Light / Dark:
- background #FAF7F0 / #1A1613   surface #FFFDF8 / #211D18   elevated #FFFFFF / #2A2521
- text #171717 / #F5F0E6   muted #525252 / #A39E95   faint #8A8578 / #78716C
- focal (primary accent) #9F1239 / #F43F5E   + focal-soft for fills/hover
- secondary / pitch #1E3A5F / #5B8AC0   + secondary-soft
- border #E5E5E5 / #34302A   grid #ECE8DF / #2A2521
Use Tailwind utilities bound to these: bg-focal, text-muted, border-strong, bg-pitch, etc.
shadcn semantic vars (--primary, --card, --ring…) are already mapped onto these tokens.

**Color law (Ticket 2):** warmth in the shell, precision in the ink. focal/secondary
are chrome ONLY — never a data mark, never a team color (that was the flip bug: 3
panels had home=focal/away=secondary, 2 had it backwards, and england's chart-theme hex
byte-for-byte collided with `secondary`). Every team-data mark reads `lib/kits.ts`'s
`kitEncoding(side, mode)` instead — one seeded kit table (Spain red `#C60B1E` /
England navy `#001E3C`, England's white shirt being unusable as ink), a two-tone chip
(`kitChip`) carrying full identity separately from the single-hue data marks, a ΔE
clash rule for future matches, and a warm-anchored heat scale (`HEAT_SCALE`, cream →
amber) decoupled from team identity. Full detail + the gallery/highlight-accent scope
carve-outs: DESIGN.md § Color & kits.

## Typography
- Display: Fraunces (serif), weight 900, optical size 144 — the `.display` class.
  Use for headlines / hero numbers, the editorial voice.
- Body: Geist Sans (--font-body).
- Mono: Geist Mono — stats, tabular figures, code.
- Type ramp (Ticket 1a): every text node uses a ramp token, never `text-[Npx]` —
  `text-display-1..4` (Fraunces sizes, clamp/34/24/20), `text-lg/base/sm` (Geist Sans,
  16/14/13), `text-mono-base/sm/xs` (Geist Mono, 12/11/10 — `mono-xs` reserve-only,
  never the sole label on an interactive control). Full table + rationale: DESIGN.md § Type.
- `text-score` (40px, fixed): added after launch — the dashboard match score used
  `display-1`'s viewport-scaled clamp (up to 56px) and read oversized next to the rest
  of the compact header. Big-number role, fixed not fluid; don't reuse `display-1` for
  widget-scale "big numbers" again.

## Shape & motion
- Radii: sm 4px, base 6px, lg 10px, xl 12px. Base token --radius 0.625rem.
- Dark mode via data-theme + next-themes; body transitions bg/color at --motion-base
  (200ms) / --ease-standard.
- Motion tokens (Ticket 1c): --motion-fast/base/slow (120/200/400ms), --ease-standard/
  out/in. Referenced as `duration-[var(--motion-*)]` (Tailwind has no named-duration
  scale) and plain `ease-standard`/`ease-out`/`ease-in` utilities. Global
  `prefers-reduced-motion: reduce` rule collapses everything to ~1ms. Tool pages get
  functional motion only; Piece pages (not built) get editorial motion. Full detail:
  DESIGN.md § Motion.
- A lint rule (`eslint-rules/no-arbitrary-design-values.mjs`) fails the build on new
  arbitrary text/spacing/duration/ease values — don't reach for `[Npx]` brackets.

## Layout notes
- Fixed TopBar height 60px (--topbar-h); mobile sticky headers offset by it.
- Site nav rail is w-60 (240px) at lg:.
- Match dashboard has **three** responsive tiers, not two (Ticket 1d): mobile tabs
  below 768px (`--breakpoint-tablet`), a real tablet tier 768–1279px, 3-column grid
  at/above 1280px (`--breakpoint-dash`, still empirical — rail + true-scale pitches
  clip below ~1000px content width, don't "simplify" it down). Center column is
  `minmax(300px, 360px)`, not a hard 360px. Named layout constants (breakpoints,
  widths, the pitch pxPerYard cap) all live in `app/globals.css`'s `@theme` block —
  full table: DESIGN.md § Spacing & layout constants.
- **Tablet tier cards are width-capped** (`--size-tablet-card`, 420px), never
  full-bleed to the viewport — the first cut (stacked, full-width cards) was a real
  shipped bug: the pitch inside stays capped at its desktop size regardless of
  container width, so a wide card was just wasted whitespace, reported as both
  "too large" and "doesn't fit the screen." Single column below 900px
  (`--breakpoint-tablet-2col`), two-up (team cards side by side, match card
  spanning below) at/above it. Use plain `1fr` grid tracks + per-card `max-width`
  for this, not a content-sized `minmax()` track — that caused a real
  ResizeObserver feedback loop with the pitch panels' own width measurement right
  at 1024px (tablet-2col × site rail's `lg:` coinciding). Full postmortem: DESIGN.md
  § Responsive tiers.
