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
- **Real bug, already fixed, don't reintroduce**: `cn()` (`lib/utils.ts`) wraps
  `tailwind-merge`, which doesn't know our custom ramp tokens — a custom `text-*` size
  class combined with a `text-{color}` class in the same `cn()` call (the completely
  ordinary `cn("text-mono-sm", isActive ? "text-focal" : "text-muted")` pattern) got
  silently dropped, and every button using it rendered at the browser's inherited 16px
  instead of 11px. Fixed once, for every call site, by registering the custom scale via
  `extendTailwindMerge`. Any *new* custom `@theme` class sharing a prefix with an
  existing Tailwind group needs the same registration or it's this bug again.
  Full postmortem: DESIGN.md § Type.

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

## `useTheme()` — a recurring gotcha, bit the codebase twice already
next-themes' `resolvedTheme` is `undefined` during SSR and the first client render
(before the stored/system preference resolves) — SSR always renders as if the theme
were "light". Reading `resolvedTheme` **directly into JSX** (an inline `style` color,
a swatch background) is a real hydration-mismatch bug for any dark-theme visitor: the
server HTML says light, the client's first render says dark, React logs a mismatch and
gives up patching that subtree. Already happened twice — `PlayerMatchAnalysisClient.tsx`
first, then `TeamColumnCard.tsx`/`MomentumBarPanel.tsx` (fix PR #17, found via a real
console error reported in dev, not a review catch). **The rule:** if a component reads
`resolvedTheme` to compute something that renders in JSX (not just inside a
`useEffect` driving D3's own imperative, client-only render), gate it behind a
`mounted` boolean set `true` in a `useEffect` — same pattern in all three files, plus
`ThemeToggle.tsx`. A theme-derived value that's only used behind interaction state
guaranteed `null`/`false` on first render (e.g. `ShotMapPanel`'s hover-only color) is
safe without the guard — checked case by case, not applied reflexively everywhere.

## Accessibility (Ticket 3)
- `--faint` re-picked on both themes for real WCAG AA contrast (5.36–5.74:1 light,
  4.65–5.51:1 dark), measured against actual surfaces, not eyeballed. Keep
  `lib/chart-theme.ts`'s `faint` in sync with `globals.css`'s `--faint` — they must
  match, chart labels and DOM text read as the same color.
- Global `:focus-visible` ring (`--focus-ring` → `--focal`) lives in `@layer
  utilities` in `globals.css`, not `@layer base` — Tailwind's `outline-none` utility
  is itself in `@layer utilities`, so a `@layer base` override loses the cascade.
- Every D3 chart panel got `role="img"` + `aria-label` (pure-picture panels) or
  `role="region"` (panels with real DOM text screen readers should read directly —
  don't use `role="img"` there, it hides the text). Live-updating readouts get
  `aria-live="polite"`; toggle controls get `aria-pressed`.
- **Known gap, not fixed:** gallery item modal (`ComponentModal.tsx`, Base UI
  `Dialog`) doesn't trap focus — Tab can escape to background content while open.
  Flagged, not silently shipped. Full detail: DESIGN.md § Accessibility.

## Social & metadata (Ticket 3)
- `metadataBase` set in `app/layout.tsx` (real domain) — required for any relative
  OG/Twitter image URL to resolve.
- **Nested routes don't inherit `openGraph`/`twitter` fields or the shared
  `opengraph-image.tsx`** — Next.js metadata merge is shallow per top-level key;
  a route that declares its own `openGraph` object replaces the parent's entirely
  (confirmed against `node_modules/next/dist/docs`, not assumed from memory). Any
  new route with its own `openGraph`/`twitter` must repeat `url`, `siteName`,
  `locale`, and `images: ["/opengraph-image"]` — don't expect them to fall through
  from the root layout.
- OG image (`app/opengraph-image.tsx`, 1200×630) fetches two Google Fonts at
  render time — Fraunces for the headline, Inter for body text — **each registered
  under its own real name and referenced by that name**. Satori has no fallback for
  a generic `"sans-serif"`/`"serif"` keyword: an unregistered family name gets
  resolved per-glyph against whatever font *is* registered, which silently produced
  mixed serif/bold text the first time this shipped. Never use a generic family
  keyword in an `ImageResponse` tree — register and name every font actually used.
  Full postmortem: DESIGN.md § Social & metadata.

## Content model (Ticket 4)
- `ComponentEntry` (`lib/components.ts`) has an optional `concept{name/source/
  link/summary}` field — lineage, where an idea came from. Unpopulated, no
  render surface yet (deferred to a future Piece page) — exists so it doesn't
  need retrofitting across 16+ components later.
- Gallery `publishedDate` was flat (one placeholder date for all 16, making the
  newest-first sort a no-op) — backfilled with each component's real
  first-commit date from `football-analytics`' git history. Real data, not
  fabricated; sort-order-only, never shown as text.
- Shared async state kit (`components/charts/AsyncState.tsx`): skeleton on
  `surface`, one-line `muted` mono error, and a distinct empty state for
  near-zero-event players (< 3 events — a real, expected outcome, never
  confused with a load failure). Applied to `PlayerMatchAnalysisClient`.
- Homepage H1: bundle flagged "Data, made visual and interactive." as generic
  and offered three tightened alternatives — Tyler reviewed, kept the original.
  Still "Data, made visual and interactive." Meta description unaffected.
