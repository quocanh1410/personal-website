# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A single-page, zero-dependency personal fun page for "Lã Quốc Anh". All UI text is Vietnamese. There is no build system, package manager, framework, or test suite — the entire site is `index.html` plus images in `photos/`.

## Running / previewing

Open `index.html` directly in a browser, or serve the folder:

```
python3 -m http.server 8000   # then visit http://localhost:8000
```

Deployment is assumed to be static hosting (GitHub Pages) — push to `main` and the site is live; there is no CI or deploy step in the repo.

## Structure

`index.html` is self-contained: `<style>` in `<head>`, `<script>` at the end of `<body>`, all decorative art is inline SVG (no external image requests except Google Fonts: Baloo 2 + Quicksand).

Four interaction systems in the trailing `<script>`, in this order:

1. **Confetti / combo / screen-shake** — `burst()`, `burstAt()`, `showCombo()`, `shakeScreen()` fire on clicking the `#nameHeading` or any `.doodle`. Elements are created, appended to `document.body`, and removed via `setTimeout` matched to their CSS animation duration — this cleanup pattern is used everywhere and must be kept when adding effects.
2. **Party mode** — `#partyBtn` toggles `body.party` (hue-rotate background) and starts a `setInterval` emoji rain; toggling off must `clearInterval`.
3. **Gallery + lightbox** — `#galleryBtn` opens `#galleryOverlay`; `buildGallery()` runs once (guarded by `galleryBuilt`) and renders from the hard-coded `photoFiles` array near the bottom of the script. Images that 404 remove their own tile via an `error` listener; if nothing loads, `checkEmpty()` injects the "no photos" message.
4. **Procedural music** — `#musicBtn` toggles a code-generated chiptune loop built with the Web Audio API (no audio files). A `setTimeout`-based lookahead `scheduler()` queues one of 32 steps (`stepIndex`) at a time onto `nextStepTime`; `scheduleStep()` layers a sine chord pad, square bass, triangle pentatonic melody (`CHORDS` / `MELODY` arrays), and oscillator/noise-buffer drums through a shared `master` gain + compressor. The `AudioContext` is created lazily on first click (autoplay policy); `startMusic`/`stopMusic` ramp `master.gain`. When `partyOn` is set, `stepSeconds()` speeds the loop up and adds a fifth above the melody.

Also present: a throttled sparkle cursor trail and a mousemove parallax that nudges `.doodle` margins.

## Conventions

- **Adding gallery photos:** drop files in `photos/` and add their paths to the `photoFiles` array in `index.html` (commented-out slots for `photos/2.png`–`6.png` are already there). The array is the single source of truth; the directory is not scanned.
- Keep everything in the single file — no external JS/CSS, no dependencies.
- `@media (prefers-reduced-motion: reduce)` disables animations; keep new animated features covered by it.
- Doodle placement/size is driven by the `.d1`–`.d8` classes and CSS custom properties (`--r` rotation, per-class `animation-duration`).
