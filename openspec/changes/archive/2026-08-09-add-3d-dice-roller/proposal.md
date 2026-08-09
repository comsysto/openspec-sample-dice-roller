## Why

This repository is the companion project for a blog post on spec-driven development with OpenSpec. It currently has no application code. A single-page 3D dice roller is a small, self-contained feature that still carries real design decisions (deterministic outcome vs. physics, face-mapping correctness, animation choreography) — making it a good first change to specify and implement end to end.

## What Changes

- Add a single static HTML page presenting one 3D die on a felt/wood surface.
- Add a "Roll" button that, on click, picks a result (1-6) and animates the die to visually land on that face.
- Render the die as a CSS 3D cube (six absolutely-positioned, transformed faces) rather than WebGL — no physics engine, no build step.
- Use Alpine.js (via CDN `<script>`) for the small amount of UI state (`rolling`, `result`), keeping the app a single `index.html` file.
- Implement realistic-looking pips per face respecting standard die conventions (opposite faces sum to 7; correct Western die chirality).
- Implement a roll animation: lift, multi-axis spin with ease-out, settle wobble, land — synchronized with a contact shadow that shrinks mid-air and snaps back on landing.
- Expose the result via an `aria-live` text region in addition to the visual face, for accessibility.
- Disable the Roll button while a roll animation is in progress.

## Capabilities

### New Capabilities
- `dice-roller`: A single-page UI that renders one realistic-looking 3D die and lets the user roll it via a button, landing deterministically on a randomly chosen face (1-6) with a physically-styled animation.

### Modified Capabilities
(none — greenfield repository, no existing specs)

## Impact

- New files only: `index.html` (or `index.html` + a small CSS/JS file) at the repository root or a dedicated `app/` directory; no existing code is touched.
- New runtime dependency: Alpine.js loaded from a CDN (no package manager, no build step).
- No backend, no persistence, no other pages — single static artifact.