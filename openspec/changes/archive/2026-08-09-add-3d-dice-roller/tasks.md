## 1. Page Scaffold

- [x] 1.1 Create `index.html` with base page structure, felt/wood surface background, and `perspective` container for the die
- [x] 1.2 Load Alpine.js from a CDN `<script>` and set up an `x-data` component with `rolling` (boolean) and `result` (number | null) state
- [x] 1.3 Add the "Roll" button, bound to trigger the roll and disabled via `:disabled="rolling"`

## 2. Die Geometry and Face Mapping

- [x] 2.1 Build the six-face cube structure (one `div` per face) positioned with `rotateX/Y` + `translateZ` inside the `perspective` container
- [x] 2.2 Implement the canonical pip layout (grid position per face value 1-6) as CSS/markup per face
- [x] 2.3 Define the fixed face-value → base rotation lookup table, verifying opposite faces sum to 7 and chirality matches standard Western dice (1→2→3 counter-clockwise from their shared corner)

## 3. Lighting and Shadow

- [x] 3.1 Apply a consistent single-light-source gradient (lighter upper-left, darker lower-right) to all six faces
- [x] 3.2 Add the contact shadow element beneath the die and define its resting (sharp/small) and lifted (blurred/larger) states

## 4. Roll Mechanics

- [x] 4.1 Implement RNG result selection (`1 + Math.floor(Math.random() * 6)`) on button click, before any animation starts
- [x] 4.2 Compute the animation's target transform from the chosen result's base rotation plus a randomized 2-3 extra full-turn spin on X and Y
- [x] 4.3 Implement the roll animation sequence: lift (shadow shrinks) → multi-axis spin (ease-out) → settle wobble (~±3° overshoot) → land (shadow snaps back)
- [x] 4.4 Set `rolling = true` for the duration of the animation and `rolling = false` (with `result` updated) on completion

## 5. Accessibility

- [x] 5.1 Add an `aria-live="polite"` text region that updates with "You rolled a {n}" when a roll completes
- [x] 5.2 Verify the Roll button remains keyboard-operable and its disabled state is conveyed to assistive tech during a roll

## 6. Verification

- [x] 6.1 Manually roll repeatedly in a browser and confirm the landed face always visually matches the announced/result value for all six outcomes
- [x] 6.2 Confirm opposite-face and chirality correctness by inspecting/rotating the die for all three opposite pairs (1-6, 2-5, 3-4)
- [x] 6.3 Confirm the Roll button cannot trigger overlapping rolls (rapid clicking during an animation has no effect)
