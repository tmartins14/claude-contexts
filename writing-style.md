# Writing Style — tylermartins.com

Voice and copy rules for all site text: homepage, section intros, view descriptions,
cards, nav labels, footers. Companion to `style.md` (visual/design tokens) — this file
covers words, that one covers pixels.

## Point of view
- First person ("I'm Tyler…") only on the homepage hero/intro.
- Everything else — section intros, view descriptions, cards, footers — is third person.

## Core rules
- **Data is the through-line.** Every subject connects back to data. Lead with what's
  built and what it shows, not credentials.
- **Curious, not salesy.** This is a personal project, not a product. Show interest,
  don't pitch.
- **Plain and concrete.** Name the actual thing ("shot maps, pass networks, xT surfaces")
  over abstract benefit language.
- **Short. Cut filler.** Punchy sentences. If a word can go, it goes.
- **Named views, clear labels.** Say what a thing is ("FootballD3 Gallery," "Match
  Analysis Dashboard") — no cute renaming.
- **Frame words:** tooling, visualization, interactivity — not "playground" or similar
  buzzwords.
- **Em dash for asides, sparingly.** One thread per sentence.
- **Attribution:** use the phrase "Built on StatsBomb open data" in prose wherever
  football data appears (section intros, card/view descriptions). This is separate from
  — and additive to — the persistent `StatsBombAttribution` badge component that already
  appears in the TopBar/Footer/gallery on every page; the prose phrase isn't a substitute
  for the badge, and the badge isn't a substitute for the phrase.

## Current canonical names
- **FootballD3 Gallery** — the component library view (`/football/components`).
- **Match Analysis Dashboard** — the single-match view (`/football/dashboard`).
