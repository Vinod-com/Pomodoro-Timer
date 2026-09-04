# Pomodoro Timer App — Project Record

## Overview
A single-file Pomodoro timer web app (index.html), hosted free on GitHub Pages.
No build tools, no dependencies, no frameworks — just one HTML file with embedded
CSS and JavaScript, plus one audio file (break.mp3) for break music.
Designed to be fully maintainable in Windows Notepad by a non-professional
programmer, with an AI assistant as coding partner.

## Design philosophy
- One master file, editable anywhere, rebuildable from this record alone
- Beauty is a feature, not an afterthought: the coin-spin ring, two-tone
  palette swatches, and the tomato story card exist because delight
  makes a tool you actually use
- Every visual oddity that survives testing is documented as *intended*,
  so a future rebuild doesn't "fix" it by accident

## Files
- index.html  — the entire app (structure, styling, logic)
- break.mp3   — music played during Break mode
-PT.txt  — this project record

## Modes
- **Focus (25 min):** countdown with the coin-spin progress ring; pausing
  freezes timer state precisely, since a Focus pause is usually urgent
- **Break (5):** countdown with spinning ring + music.
  Design decision: when pause is pressed mid-break, the ring is allowed to
  finish its rotation and settle gracefully (1–2 extra seconds). Intended:
  pausing a break means the user wants to linger, so a graceful glide suits
  the mood and costs nothing
- **Stopwatch:** counts up; originally used to time how long the app took
  to build

## Features
- Coin-spin progress ring encircling the digits (the app's signature visual;
  original animation kept after experiments with alternatives)
- "Sessions completed" counter — Pomodoro tracking with a celebratory feel
- Pause / Reset / Start controls
- Adjustable theme: dark/light toggle (🌙 button) + six color palettes
- Clock display area (--:--) that toggles between date and time on tap
  (toggleClockMode)
- Story feature: a 🍅 at the bottom of the page — click (or hover) to open a
  scrollable card telling the history of the Pomodoro technique
  (Francesco Cirillo, 1980s, the tomato kitchen timer)
- Persisted preferences: chosen theme and accent survive page reloads

## Color system (architecture)
- Root element carries two attributes: data-theme ("dark"/"light") and
  data-accent (palette name); CSS variables do all the theming:
  --bg, --fg, --muted, --surface, --surface2, --border, --accent, --accent-fg
- Base palettes for :root[data-theme="dark"] and "light" apply when no
  accent overrides them
- Each named accent defines a base line, plus full light and dark variable
  sets — so every palette is really two complete themes
- --accent-fg is the text colour used ON accent-col buttons; on bright
  accents it must be dark (e.g. dark-on-gold), not white, or buttons
  become unreadable

## The six palettes
1. claude  — terracotta (#d97757); light-mode accent-fg darkened
2. gemini  — blue; light mode: pale blue bg, deep blue fg; dark mode:
   navy surfaces with lighter sky-blue accent
3. sage    — green; misty light / deep forest dark
4. plum    — purple; lavender light / aubergine dark
5. gold    — dark mode: deep goldenrod #DAA520 digits/ring on dark bronze
  1F1A05 (jewellery look); light mode: deep gold-brown #7a5c10 on warm
   cream #FAF3E0. Chosen after user feedback: #FFD700 read as "yellow,"
   not gold — deepening toward orange-brown fixed it. Lesson: true gold
   on screens = goldenrod family, and bright gold is unreadable on white
6. obsidian — dark mode: near-black #0D0D0D bg with gray #D3D3D3 fg;
   light mode: definite gray #E4E4E4 bg with charcoal #2b2b2b fg. Deepened
   after user feedback that the original white bg was "whitish," not gray
- Swatch chips: diagonal two-tone previews — linear-gradient(135deg,
  light-color 50%, dark-color 50%) — one glance shows both themes
- Hover effect (scale 1.15); active palette outlined with var(--fg)

## Recipe: adding a new palette (three steps, zero JavaScript)
1. CSS: three :root[data-accent="NAME"] lines after the last palette —
   base (--accent + --accent-fg), light variant, dark variant, each with
   the full variable set
2. HTML: one button in the palette div — class "swatch sw-NAME",
   data-accent="NAME", onclick="setAccent('NAME')", aria-label
3. CSS: one .sw-NAME two-tone gradient rule matching the swatch house style
(setAccent and toggleTheme are generic; they need no edits)

## Key fixes made during development
- Mode buttons: CSS flex adjusted so "Stopwatch" label fits without wrapping
- Progress ring: kept the original coin-spin animation after experiments
  with alternatives
- iOS audio unlock: added an unlock block so Break music plays on iPhones
  (requires silent switch off + a user tap to start); CONFIRMED working
on a real iPhone in Break mode
- Tooltip/story card: plain background colours; :focus rule so a click
  keeps the card open for scrolling; scrollable card with max-height on
  small screens
- Gold palette: #FFD700 → #DAA520 after real-use feedback (yellow → gold)
- Obsidian light: #FFFFFF → #E4E4E4 after real-use feedback (white → gray)
- Early theme-system misfire new palettes were first added as standalone
  .theme-CLASS blocks, which silently did nothing because the app uses
  data-attributes, not classes. Lesson: read the existing pattern FIRST,
  then extend it

## Editing workflow
Edit locally in Notepad → Ctrl+S (plain, never Save As, to preserve
the .html extension) → drag file into GitHub repository → Commit →
site updates live in 1–2 minutes → always test in a FRESH TAB to dodge
cached versions. Double-click runs the app locally; right-click →
Open with → Notepad edits it.

## Lessons learned
- One master file (index.html), edited directly — no .txt copies, to avoid
 -copy confusion
- Match the codebase's existing patterns before adding anything new
- Choose colours for readability in context, not beauty in isolation
  (gold on white teaches this twice)
- Collect real-use feedback before finalising aesthetics — the user's eye
  caught what the design phase missed, twice
- Document intended quirks (like the break-ring settle) so they survive
  future rebuilds
- Verification habit: after every edit, Ctrl+F for old and new strings
  to confirm counts before committing

