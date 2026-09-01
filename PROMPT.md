# PROMPT.md — Pomodoro Timer Project Context

## Project
Single-file `index.html` Pomodoro + Stopwatch web app (HTML + CSS + vanilla JS,
no build tools, no dependencies). Deployed on GitHub Pages. Companion file:
`break.mp3` (break music) in the repository root, next to `index.html`.

## Status: COMPLETE AND WORKING ✅

## Features
- Three modes: Focus countdown, Break countdown, Stopwatch (with laps/splits)
- Drift-proof countdown: uses `endAt = Date.now() + remaining * 1000`
  (wall-clock target, immune to background-tab throttling)
- Per-mode state preservation: switching Focus ↔ Stopwatch, not loses
- Dark/light toggle (🌙/☀️), persisted to localStorage
- 4 color palettes (Claude orange, Gemini blue, Sage green, Plum) via
  `data-accent` + `data-theme` attributes on CSS variables
- Tick sound toggle: soft 1200Hz square-wave click per second, deduped via
  `lastWholeSec`
- Break music: plays `break.mp3` via `<audio id="breakmusic">` element,
  started in `advancePhase()` when entering break, stopped when leaving
- Ring spin animation: on entering Break, the big circle continuously spins
  like a turning coin (horizontal rotation, `rotateY`), via CSS class
  `.ring-wrap.spinning` + `@keyframes spinNS` (one revolution every 6s;
  adjust the `6s` value to change speed). Spin is removed on leaving break
  or switching modes (`setMode()` toggles the class).
- Persistence: theme, accent, durations, sessions count, tick/music toggles
  saved to localStorage
- Tab title countdown with ▶/⏸ indicators
- PWA metas + SVG data-URI favicon
- Zero-duration edge cases handled (auto-advance or clean stop)

## Key architecture points
- Single shared AudioContext, created lazily via `getCtx()`, unlocked on
  first user gesture via `unlockAudio()`
- `setSessions()` is the single path for session count updates
- `applyDurations()` pauses the timer on any duration edit (prevents stale
  `endAt` overwriting the edit)
- `savePaused()` stores countdown state per mode
- `advancePhase()` removes/re-adds the `spinning` class on `#ringwrap` when
  entering break
- `setMode()` contains:
  `document.getElementById('ringwrap').classList.toggle('spinning', mode === 'break');`

## Fix history (chronological)
1. Broken meta tag → fixed to `black-translucent`
2. `let endAt = 0;` declaration
3. `musicNodes` declared separately
4. `splitToInputs('break', breakSecs)` typo fixed
5. `startInterval`/`stopTimer` corruption repaired
6. `setValueAtTime(0.0001, now)` in startMusic
7. `setMusicOn(on)` calls `stopMusic()` when false
8. `unlockAudio()` + tick/music toggle persistence added
9. Sage dark theme selector scoped to `[data-theme="dark"]`
10. Removed dead `saveCountdownState()` function
11. `applyDurations()` calls `stopTimer()` on duration change
12. Break music switched from generated pad to `break.mp3` file via
    `<audio id="breakmusic" src="break.mp3" loop preload="auto">`
13. Spin animation: replaced one-shot `flipNS`/`rotateX` flip with
    continuous `spinNS`/`rotateY` (horizontal coin-spin), active only
    during Break mode

## Deployment notes
- `index.html` and `break.mp3` both live in the TOP LEVEL of the repo
- MP3 filename is case-sensitive on GitHub Pages: must be exactly
  `break.mp3` (verified — not `break.mp3.mp3`)
- Upload binary files (MP3) via Add file → Upload files; never paste them

## User's workflow
- Local file kept as `POMODORO-Timer Laptop 1Sep2026(1).txt`
- Non-expert; prefers targeted find/replace snippets, NOT full-file resends
- Update this PROMPT.md before requesting code changes
