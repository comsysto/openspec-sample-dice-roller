## Context

Greenfield repository — no existing UI code, no build tooling, no framework choice made yet. This change is also meant to serve as a companion demo for a blog post about spec-driven development, so the implementation should stay small enough to read end-to-end and closely mirror what this design document specifies.

## Goals / Non-Goals

**Goals:**
- A single die that looks and moves like a real physical die when rolled, using only CSS 3D transforms (no WebGL, no physics engine).
- Deterministic, correct outcomes: the face the animation lands on always matches the reported result.
- Correct die conventions (opposite-face sum, chirality) so the die doesn't look subtly "wrong" to anyone who has held real dice.
- Minimal runtime footprint: one HTML file, one CDN dependency, no build step.
- Basic accessibility: the result is available as text, not only as a visual face orientation.

**Non-Goals:**
- No physics simulation (no rigid-body engine, no organic/emergent outcome).
- No roll history, statistics, multiple dice, or configurable die types (d4/d20/etc.) — single d6 only.
- No backend, persistence, or multiplayer/shared state.
- No drag-to-roll or keyboard-triggered roll in this iteration — button click only.
- No theme switcher — one realistic visual style (ivory body, black pips) is the only style shipped.

## Decisions

### 1. Fake 3D via CSS transforms, not WebGL
A cube built from six `div` faces inside a `perspective` container, each positioned with `transform: rotateX/Y(...) translateZ(...)`. Rejected WebGL/Three.js: it would require a physics or tweening library to look convincing, adds a real dependency, and turns "what determines the outcome" into a much bigger design question (see Decision 3). CSS transforms keep the whole visual behavior inspectable directly in the stylesheet and animation keyframes.

### 2. Alpine.js for state, no build step
State needed is small: `rolling` (boolean) and `result` (1-6 or null). Alpine.js (`x-data`, `@click`, `:class`, `x-text`) expresses this declaratively in plain HTML, loaded from a CDN `<script>` tag — no `npm`, no bundler, app stays one `index.html`. Rejected Vue/React/Svelte: all require or strongly favor a build step for a component this small, which would work against the "single inspectable artifact" goal. Rejected vanilla JS with no framework: viable, but the proposal calls for a small framework and Alpine's declarative bindings keep the HTML/behavior mapping more explicit than manual `querySelector`/`addEventListener` wiring.

### 3. Result-first, then animate to match (no physics)
On click, the result is chosen immediately via `Math.random()` (`1 + Math.floor(Math.random() * 6)`). The animation's final transform is then computed from that result and the die always visibly lands on the correct face. Rejected "let physics decide": with a fake-3D (non-physical) cube, there's nothing to actually simulate — an emergent-outcome approach only makes sense with a real physics engine, which Decision 1 rules out. Rolling the result first also removes any risk of the visual face and the reported result disagreeing.

### 4. Fixed face-to-rotation mapping with correct chirality
Faces are assigned to cube sides using the standard convention that opposite faces sum to 7 (1↔6, 2↔5, 3↔4), and pip layout/face assignment follows Western die chirality (viewed from the 1-2-3 corner, 1→2→3 runs counter-clockwise). This mapping is a fixed lookup table (face value → base `rotateX/rotateY` to bring that face to the front/up position), not computed generically, since the two chiralities of dice are mirror images and only one reads as "correct" to anyone familiar with real dice.

### 5. Realism via a fixed lighting/shadow recipe, not a material library
"Realistic" is approximated with: a single implied light source (CSS gradient highlight upper-left / darker lower-right, applied identically to all six faces so the cube reads as one consistent object), and a blurred elliptical contact shadow beneath the die whose blur/size animates with the die's implied lift (shrinks & blurs more mid-roll, snaps back sharp on landing). This is deliberately a fixed, small set of CSS values rather than a generalized theming system, since only one visual style ships (Non-Goal).

### 6. Roll choreography as a single CSS animation driven by a computed end-state
Sequence: lift (translateZ up, shadow shrinks) → multi-axis spin (2-3 extra full 360° turns on X and Y layered on top of the target face's base rotation, eased out) → settle wobble (~±3° overshoot before resting) → land (translateZ down, shadow snaps back). Implemented as a CSS transition/animation whose target transform is set via an inline style or CSS custom property computed from the chosen result, keeping the keyframe timing shared across all six possible outcomes.

### 7. Accessibility: aria-live text result alongside the visual face
An `aria-live="polite"` region announces "You rolled a {n}" when a roll completes, in addition to the visual pip face. The Roll button is `disabled` (not just visually inert) for the duration of the animation to prevent overlapping rolls and to give assistive tech a clear busy state.

## Risks / Trade-offs

- **[Risk]** CSS-only lighting can look flat compared to real lighting/PBR → **Mitigation**: accept as a known limitation of the fake-3D approach (Decision 1 trade-off); the contact shadow and gradient highlight are the two levers doing most of the "realism" work and are worth tuning carefully.
- **[Risk]** Predetermined-then-animated rolls could look mechanical if the spin math is naive (e.g., always the same number of turns) → **Mitigation**: randomize the extra spin count/direction within the 2-3 turn range per roll, independent of the RNG result, so repeated rolls of the same face don't look identical.
- **[Risk]** `prefers-reduced-motion` users may find a ~1-1.5s multi-axis spin uncomfortable → **Mitigation**: out of scope for this change's Non-Goals, but flagged here as a reasonable fast-follow (respect `prefers-reduced-motion` by shortening/skipping the spin while still landing on the correct face).
- **[Trade-off]** Alpine.js from a CDN means the page requires network access to load the framework on first paint → acceptable given the project's "no build step" goal; a future iteration could vendor the script if offline use matters.

## Open Questions

- Exact color/material theme beyond "ivory body, black pips" (e.g., is a felt or wood surface preferred) — defaulted to classic ivory/black + green felt per exploration; can be revisited without affecting requirements below.
- Whether `prefers-reduced-motion` support should be pulled into this change's scope or deferred (currently deferred; see Risks).