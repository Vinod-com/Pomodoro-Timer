# VIBE PROMPT — "The Spinning Ring Pomodoro Timer"

## Vision
Build a complete, single-file Pomodoro timer web app (one index.html containing
HTML + CSS + JavaScript inline, no build tools, no external dependencies).
The signature feature — the thing a user must remember it by — is a large
ANIMATED RING that spins continuously during Break mode around the countdown
digits. Design philosophy: never cut a motion off mid-sentence. All animations
should finish gracefully and park (like a car windscreen wiper that completes
its cycle before stopping, even when switched off mid-wipe).

## Core features
1. THREE MODES: Focus (countdown), Break (countdown), Stopwatch (counts up).
   - Mode selector tabs. Switching modes is only natural when fresh (not mid-session).
   - Start/Pause single toggle button with label updating ("Start" / "Pause").
2. FOCUS / BREAK COUNTDOWN:
   - Configurable focus and break durations (default 25 min / 5 min).
   - Drift-proof countdown: always compute remaining time from a wall-clock
     target timestamp (endAt = Date.now() + remaining*1000), NEVER by counting
     interval ticks. This keeps it accurate when the browser throttles the tab
     in the background (critical on mobile).
   - Phase-fresh logic: entering a paused 0-second phase auto-advances to the
     next sensible phase before starting.
3. THE RING:
   - SVG or CSS ring around the digits. During Break it spins via a CSS
     animation (class "spinning"). Spin must be SLOW enough that the countdown
     digits remain readable as the ring passes them (~4–5s per rotation).
   - FILL: the ring fills/depletes proportionally to the focus/break progress.
   - PAUSE BEHAVIOUR (the wiper trick): when pausing during Break, the digits
     freeze instantly, but the ring must COMPLETE ITS CURRENT ROTATION and then
     park upright (digits readable). Implementation that works reliably on
     mobile: attach ONE animationiteration listener to the ring element early
     (arm once, e.g. via a dataset flag, the first time pause is pressed).
     In the listener: if the timer is not running, remove the "spinning" class.
     This guarantees the ring completes only the remaining part of the current
     turn — never a bonus lap. If the user presses Start before the turn ends,
     the guard (if (!timer)) lets the ring keep spinning with no stutter.
   - Note: do NOT attempt to compute leftover rotation with getAnimations()/
     currentTime — unreliable on mobile browsers. The armed event listener is
     the bulletproof approach.
4. SOUND:
   - Tick sound each second in Focus mode (subtle, optional).
   - Completion chime when a phase ends.
   - Optional ambient BREAK MUSIC via <audio> element with a GENERATIVE
     FALLBACK (Web Audio synthesized pad) if the audio file is missing — the
     app must never break because an asset is absent.
   - Music only plays during Break, only if the music toggle is checked, and
     only while the timer is running.
   - iOS/Safari: unlock AudioContext on the FIRST user tap (resume it inside
     the first button press handler) or no sound will play on iPhone.
5. LIVE CLOCK: current time (and optionally date) displayed on the page,
   toggleable, positioned so it never collides with the ring on small screens.
6. THEME: dark/light theme toggle + accent colour options. Persist all user
   preferences AND completed-session count in localStorage.
7. UI POLISH: session counter, phase label ("Focus"/"Break"), page title
   showing remaining time while running, responsive layout for phone and
   laptop, dark and light variants both fully readable.

## Known pitfalls (learned the hard way — avoid these)
- Duplicated closing tags/braces from partial edits can silently kill the
  whole script — always verify brace/bracket balance after editing.
- CSS class toggles control the animation; adding an already-present class
  is harmless, so resume code needs no special-casing.
- When testing, ALWAYS hard-refresh / use a fresh tab: stale cache will
  masquerade as a bug (the "cache phantom").
- Before reporting a bug, verify the observation carefully: a ring at 180°
  shows mirrored digits and LOOKS like a full turn. The digits are the
  truth-teller: mirrored = still mid-turn, upright = parked.
- During the settle pause, digits freeze while the ring finishes its turn —
  this brief mismatch is intentional (speedometer needle settling).

## Acceptance test (run after building)
1. Start Focus, digits count down, ring fills, title updates.
2. Pause: digits freeze immediately, ring stays put, button reads "Start".
3. Start Break: ring spins continuously, digits readable.
4. Pause mid-spin: digits freeze, ring completes ONLY the remaining part of
   the current turn, parks upright with readable digits.
5. Quick Pause→Start within one rotation: ring never stops at all.
6. Break completes: chime sounds, app advances to next phase.
7. Reload page: preferences, theme, and session count persist.
8. Everything above works on a phone browser (touch, small screen).
