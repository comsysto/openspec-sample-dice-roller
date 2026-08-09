## ADDED Requirements

### Requirement: Display a Single 3D Die
The system SHALL render a single six-sided die as a CSS 3D object on a single static page, at rest, before any roll is triggered.

#### Scenario: Initial page load
- **WHEN** the page finishes loading
- **THEN** a single die is visible, rendered as a 3D cube with pips showing on the currently front-facing side, resting on a surface with a visible contact shadow

### Requirement: Roll the Die via Button
The system SHALL provide a single "Roll" button that, when clicked, triggers a roll animation of the die.

#### Scenario: User clicks Roll
- **WHEN** the user clicks the "Roll" button while no roll is in progress
- **THEN** the die begins a roll animation (lift, multi-axis spin, settle, land)

#### Scenario: Button disabled during animation
- **WHEN** a roll animation is in progress
- **THEN** the "Roll" button is disabled, and clicking it (or pressing it again) has no effect until the current animation completes

### Requirement: Deterministic Outcome Matching Visual Face
The system SHALL determine the roll's numeric result before animating, and the animation SHALL always end with that exact result's face oriented as the visible top/front face of the die.

#### Scenario: Result matches landed face
- **WHEN** a roll animation completes
- **THEN** the pip pattern visible on the die's resting face equals the numeric result that was chosen for that roll, with no possible mismatch between result and displayed face

#### Scenario: Result is uniformly random
- **WHEN** a roll is triggered
- **THEN** the result is selected as an integer from 1 to 6 with no fixed bias toward any particular value

### Requirement: Standard Die Face Conventions
The system SHALL lay out the die's six faces so that opposite faces sum to 7 (1↔6, 2↔5, 3↔4) and so that the pip arrangement matches standard Western die chirality (viewed from the corner where faces 1, 2, and 3 meet, the values 1→2→3 SHALL run counter-clockwise).

#### Scenario: Opposite faces sum to seven
- **WHEN** any two opposite faces of the rendered die are inspected (e.g. by rotating it in the browser's dev tools or via the defined face-to-rotation mapping)
- **THEN** their pip values sum to 7

#### Scenario: Correct chirality
- **WHEN** the die is viewed from the corner shared by faces 1, 2, and 3
- **THEN** the values 1, 2, 3 are arranged counter-clockwise, matching standard Western dice

### Requirement: Realistic Visual Rendering
The system SHALL render the die and its animation using a consistent single-light-source lighting effect and an animated contact shadow, so the die reads as a physical object rather than a flat graphic.

#### Scenario: Consistent lighting across faces
- **WHEN** any face of the die is visible, whether at rest or mid-roll
- **THEN** it shows the same implied light-source gradient (lighter toward one consistent corner, darker toward the opposite corner) as every other face

#### Scenario: Shadow responds to roll motion
- **WHEN** a roll animation plays
- **THEN** the die's contact shadow visibly shrinks and blurs during the "lift" phase of the animation and returns to its sharp, resting size and blur when the die lands

### Requirement: Accessible Result Announcement
The system SHALL expose the roll result as text in an `aria-live` region, in addition to the visual die face, so the outcome is available to assistive technology.

#### Scenario: Screen reader announcement on roll completion
- **WHEN** a roll animation completes
- **THEN** an `aria-live` region's text is updated to announce the numeric result (e.g. "You rolled a 4")
