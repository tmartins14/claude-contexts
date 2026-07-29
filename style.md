# Design & Presentation Style — Shared

The look for tylermartins.com and the football-analytics viz. Source of truth for
tokens is `tylermartins.com/app/globals.css` (Tailwind v4, CSS-first). This file is
the human-readable intent — when they diverge, globals.css wins for values.

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

## Typography
- Display: Fraunces (serif), weight 900, optical size 144 — the `.display` class.
  Use for headlines / hero numbers, the editorial voice.
- Body: Geist Sans (--font-body).
- Mono: Geist Mono — stats, tabular figures, code.

## Shape & motion
- Radii: sm 4px, base 6px, lg 10px, xl 12px. Base token --radius 0.625rem.
- Dark mode via data-theme + next-themes; body transitions bg/color at .25s.
- Animation via tw-animate-css. Keep motion purposeful — reveal/transition to guide
  the eye through the analysis, not decoration.

## Layout notes
- Fixed TopBar height 60px (--topbar-h); mobile sticky headers offset by it.
- Site nav rail is w-60 (240px) at lg:.
- Match dashboard: 3-column grid above the `dash` breakpoint (80rem), tabbed mobile
  stack below. Center column is a fixed 360px. The 80rem threshold is empirical (rail
  + fixed columns clip below ~1000px content width) — don't "simplify" it to 720px.
