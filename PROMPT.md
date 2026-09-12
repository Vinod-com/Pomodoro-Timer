[PROMPT.md](https://github.com/user-attachments/files/32141825/PROMPT.md)
# Pomodoro Timer App — Project Record

## Overview
A single-file Pomodoro timer web app (index.html), hosted free on GitHub Pages.
No build tools, no dependencies, no frameworks — just one HTML file with embedded
CSS and JavaScript, plus one audio file (break.mp3) for break music.
Designed to be fully maintainable in Windows Notepad by a non-professional
programmer, with an AI assistant as coding partner.

## Design philosophy
- One master file, editable anywhere, rebuildable from this record alone
- Beauty is a feature, not an afterthought: the coin-spin ring, the gold-bangle
  ring during breaks, the sparkle field, two-tone palette swatches, and the
  tomato story card exist because delight makes a tool you actually use
- Every visual oddity that survives testing is documented as *intended*,
  so a future rebuild doesn't "fix" it by accident

## Files
- index.html  — the entire app (structure, styling, logic)
- break.mp3   — music played during Break mode
- PROMPT.md   — this project record

## Modes
- **Focus (25 min):** countdown with the coin-spin progress ring; pausing
  freezes timer state precisely, since a Focus pause is usually urgent.
  The ring fills as a flat accent-colored (or gold-gradient, see below) band.
- **Break (5):** countdown with spinning ring + music.
  Design decision: when pause is pressed mid-break, the ring is allowed to
  finish its rotation and settle gracefully (1–2 extra seconds). Intended:
  pausing a break means the user wants to linger, so a graceful glide suits
  the mood and costs nothing.
  While a break is active, the completed focus ring: stays fully filled,
  spins in 3D (see "3D tube ring" below), and is surrounded by a sparkle
  field (see "Sparkle field" below). All three end together the instant
  Focus starts again.
- **Stopwatch:** counts up; originally used to time how long the app took
  to build

## Features
- Coin-spin progress ring encircling the digits (the app's signature visual;
  original animation kept after experiments with alternatives)
- **3D tube ring (break only):** the completed ring switches from a flat
  stroke to a fake rounded-tube cross-section — three concentric circles
  (dark base, mid-tone/gradient band, thin bright highlight arc) sharing
  one centerline, so the wider base peeks out on both edges of the
  narrower layers on top. Combined with the ring's existing 3D `rotateY`
  spin, this reads as a real dimensional bangle turning in space rather
  than a flat circle rotating. Reverts to the flat ring the moment Focus
  restarts.
- **Sparkle field (break only):** a dense scatter of very small glinting
  dots around the inner edge of the gold band — see its own section below.
- **Gold ring gradient:** when the gold palette is active, the ring's fill
  (flat and 3D versions both) uses a metallic diagonal gradient instead of
  a flat color, so it reads as gold catching light from one direction
  rather than a solid mustard band. Other palettes keep a flat accent color.
- "Sessions completed" counter — Pomodoro tracking with a celebratory feel
- Pause / Reset / Start controls
- Adjustable theme: dark/light toggle (🌙 button) + six color palettes
- Clock display area (--:--) that toggles between date and time on tap
  (toggleClockMode)
- Story feature: a 🍅 at the bottom of the page — click (or hover) to open a
  scrollable card telling the history of the Pomodoro technique
  (Francesco Cirillo, 1980s, the tomato kitchen timer)
- Persisted preferences: chosen theme and accent survive page reloads
- Completion flash: when a phase ends, the ring flashes (glow pulse, 4
  iterations at 0.5s each) before settling into the next phase's visual
  state — user feedback: "looks very cool and impressive," kept as-is

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
- The ring itself is drawn as SVG circles (not CSS), independent of the
  --accent/--accent-fg system used for buttons and text: `.fill` (flat,
  used during Focus and as the default Break look) and `#fillTube` (the
  3D version, shown only during Break — see below)

## The six palettes
1. claude  — terracotta (#d97757); light-mode accent-fg darkened
2. gemini  — blue; light mode: pale blue bg, deep blue fg; dark mode:
   navy surfaces with lighter sky-blue accent
3. sage    — green; misty light / deep forest dark
4. plum    — purple; lavender light / aubergine dark
5. gold    — dark mode: deep goldenrod #DAA520-family digits/ring on dark
   bronze 1F1A05 (jewellery look); light mode: deep gold-brown #7a5c10 on
   warm cream #FAF3E0. Chosen after user feedback: #FFD700 read as
   "yellow," not gold — deepening toward orange-brown fixed it. Lesson:
   true gold on screens = goldenrod family, and bright gold is unreadable
   on white. The ring itself goes further — see "Gold ring gradient" below
6. obsidian — dark mode: near-black #0D0D0D bg with gray #D3D3D3 fg;
   light mode: definite gray #E4E4E4 bg with charcoal #2b2b2b fg. Deepened
   after user feedback that the original white bg was "whitish," not gray
- Swatch chips: diagonal two-tone previews — linear-gradient(135deg,
  light-color 50%, dark-color 50%) — one glance shows both themes
- Hover effect (scale 1.15); active palette outlined with var(--fg)

## Gold ring gradient
- An SVG `<linearGradient id="goldGradient">` (diagonal, x1=15% y1=0% to
  x2=85% y2=100%) with warm-gold stops — no pure/near-white stops, after
  user feedback that early versions read as too white. Current stops run
  through `#EFCE7C → #DDAE45 → #B4842A → #7A5814 → #D4A340 → #E9C978 →
  #DDAE45`, giving a light-to-shadow-to-light sweep across the ring's
  bounding box, which on a stroked circle reads as one side catching
  light and the other in shadow — the standard trick for a curved metal
  surface.
- Applied via `:root[data-accent="gold"] .fill { stroke: url(#goldGradient); }`
  and the equivalent rule for `.tube-mid` in the 3D version. Every other
  palette keeps a flat `var(--accent)` stroke.
- A soft warm `drop-shadow` accompanies the gradient on both the flat and
  3D gold rings for extra shine.

## 3D tube ring (break only)
- Markup: inside the same `<svg>`, alongside the existing flat `.fill`
  circle, a `<g id="fillTube">` holds three concentric circles at the
  same center and radius (r=112) but different stroke widths:
  `.tube-base` (widest, stroke-width 15, `filter: brightness(0.5)` to
  darken whatever the current accent is), `.tube-mid` (stroke-width 10,
  carries the gold gradient when gold is active, otherwise flat accent),
  and `.tube-hi` (stroke-width 3, a thin near-white/warm-white arc, only
  ~16% of the circumference via `stroke-dasharray` so it reads as one
  glint rather than a second full ring).
- Because the base is wider than the layers on top of it, a sliver of the
  darkened base shows on both the inner and outer edge of the narrower
  mid/highlight layers — the classic technique for making a flat stroke
  read as a rounded tube in cross-section.
- Visibility is pure CSS: `#fillTube` is hidden by default;
  `.ring-wrap.ring3d #fill { visibility: hidden }` and
  `.ring-wrap.ring3d #fillTube { visibility: visible }` swap which one
  shows. Nothing about the flat `.fill` element itself (its dashoffset,
  its fill-fraction logic) changes — the 3D version is a pure visual
  overlay, always kept at the "complete circle" state, which is why it
  only ever needs to appear during Break.
- Combined with the ring-wrap's existing `rotateY` 3D-perspective spin
  (already used for the break-time "coin flip" look), the tube shading
  plus the rotation together genuinely read as a dimensional object
  turning in space, not a flat circle spinning.

## Sparkle field (break only)
- Technique: a single CSS `box-shadow` list can hold many independent
  tiny "dots" cheaply — far simpler than generating that many DOM
  elements or radial-gradient stops. Two 1-2px pseudo-elements
  (`.ring-wrap.sparkle::before` and `::after`) each carry one dot list,
  fed via CSS custom properties (`--sparkle-dots-a` / `-b`) set from
  JavaScript. Two lists with different animation timings (2.1s vs 2.7s,
  offset by 0.55s) avoid one flat unison pulse and instead read as
  scattered, independent twinkling.
- Placement: dots are scattered mostly just inside the inner edge of the
  gold band, with fewer sitting right on the band, and none past its
  outer edge — radius range r=96 to r=118 (in the ring's own 260×260 SVG
  coordinate space), skewed toward the inner end via
  `Math.pow(Math.random(), 1.8)` so most land close to 96 and only a
  minority reach out to 118. This range was tuned twice from user
  feedback: first pulled in from an initial deeper 85–118 spread that
  looked like "added decorative lighting" rather than the ring itself
  sparkling; current 96–118 halves that inward reach.
- Size and density were also tuned iteratively from user feedback,
  trending smaller and denser each round. Current values: dot size
  ~0.015–0.065 (scaled, see below), 380 dots per layer (760 total).
  Both numbers are easy single-line changes if you want to go further in
  either direction — see `makeDots(count, scale)` in the script.
- **Responsive fix:** box-shadow offsets are real screen pixels, unlike
  the SVG ring itself (which scales automatically via its `viewBox`).
  Early versions computed dot positions assuming the ring's 260px
  desktop design size, so on a different actual render size (mobile,
  a resized window, an artifact preview pane) the dots drifted out of
  alignment with the band — sometimes spilling outside the ring
  entirely. Fixed by reading the ring's *actual* `clientWidth` at
  runtime and scaling every dot's radius and size by
  `clientWidth / 260`. A `ResizeObserver` on the ring-wrap regenerates
  the whole sparkle field whenever that size changes (load, window
  resize, orientation change), so the sparkles now stay locked to the
  band on any screen instead of matching only one specific size.
- Color mix: ~45% of dots use a warm near-white `rgba(255,250,222,…)`
  for glint variety, the rest use `var(--accent)`.

## `setBreakVisuals(on)` helper
- The sparkle field and the 3D tube ring must always turn on and off
  together with Break mode. Rather than toggling two CSS classes
  (`sparkle`, `ring3d`) separately at every place the app can enter or
  leave Break — the auto phase-advance, manually switching tabs, and
  both branches of the manual Start/Pause toggle when starting a fresh
  phase — a single `setBreakVisuals(on)` function toggles both classes
  together. All four call sites now go through it, so it's structurally
  impossible for the sparkle field and the 3D ring to fall out of sync.

## Recipe: adding a new palette (three steps, zero JavaScript)
1. CSS: three :root[data-accent="NAME"] lines after the last palette —
   base (--accent + --accent-fg), light variant, dark variant, each with
   the full variable set
2. HTML: one button in the palette div — class "swatch sw-NAME",
   data-accent="NAME", onclick="setAccent('NAME')", aria-label
3. CSS: one .sw-NAME two-tone gradient rule matching the swatch house style
(setAccent and toggleTheme are generic; they need no edits. The gold
gradient and 3D tube ring are scoped to `[data-accent="gold"]` and are
opt-in per palette — a new palette defaults to the plain flat ring unless
you add its own gradient the same way.)

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
- Gold ring gradient: first pass used near-white highlight stops, which
  user feedback again called too white — pulled the whole stop set toward
  warmer mid-gold tones, same lesson as the original goldenrod fix
- Sparkle field: first version used only 7 fixed radial-gradient dots at
  fixed positions — too sparse. Rebuilt as a box-shadow-based field that
  scales cheaply to hundreds of dots; then tuned smaller/denser and
  repositioned closer to the band across several rounds of feedback
- Sparkle field going out of alignment between devices/windows was a
  units bug, not a code-branch difference (there is only ever one file):
  box-shadow px vs. SVG viewBox units. Fixed with a live clientWidth-based
  scale factor plus a ResizeObserver — see "Sparkle field" above

## Editing workflow
Edit locally in Notepad → Ctrl+S (plain, never Save As, to preserve
the .html extension) → drag file into GitHub repository → Commit →
site updates live in 1–2 minutes → always test in a FRESH TAB to dodge
cached versions. Double-click runs the app locally; right-click →
Open with → Notepad edits it.

## Lessons learned
- One master file (index.html), edited directly — no .txt copies, to avoid
  copy confusion
- Match the codebase's existing patterns before adding anything new
- Choose colours for readability in context, not beauty in isolation
  (gold on white teaches this twice; the gold ring gradient a third time)
- Collect real-use feedback before finalising aesthetics — the user's eye
  caught what the design phase missed, repeatedly, on both color and on
  the sparkle field's size/density/placement
- Document intended quirks (like the break-ring settle) so they survive
  future rebuilds
- Verification habit: after every edit, Ctrl+F for old and new strings
  to confirm counts before committing
- CSS box-shadow positions are real screen pixels; SVG viewBox content is
  not. Any effect that mixes the two needs an explicit, live-measured
  scale factor, or it will only look right at one specific render size
- When two visual effects must always change state together (sparkle +
  3D ring), give them one shared toggle function rather than duplicating
  the pair of class names at every call site — it's the only way to
  guarantee they can't drift out of sync as the code grows
