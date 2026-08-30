# Test Plan

## Purpose

This document defines repeatable verification for Craic the Clef. It is both a plan for the future automated suite and a manual release checklist for behavior and appearance that benefit from human judgment.

Do not mark an item complete unless it was exercised for the current change. Record the date, browser version, viewport/device, and tester in a release note or pull request once the project has that workflow.

## Test policy

- Testable behavior is developed with strict red–green–refactor TDD.
- Unit tests cover deterministic domain logic and should form most of the suite.
- Functional tests cover DOM rendering, input, accessibility state, and session transitions.
- E2E tests cover a small number of critical user journeys in real Chrome.
- Visual layout is checked manually through a future browser test harness; pixel-perfect screenshot tests are avoided unless a stable, high-value case later emerges.
- Randomness and clocks are replaced with controlled inputs in automated tests.
- Chrome is required. Edge is best-effort. Other browsers are informational only.

## Planned automated coverage

No automated infrastructure exists yet. Introducing it is a separate, TDD-scoped change. Tool choice should remain minimal and be recorded in an architecture decision if it adds dependencies.

### Unit tests

- Pitch creation preserves letter, accidental/spelling when introduced, octave, and stable identity.
- Diatonic numbering is correct across octave boundaries.
- E4 maps to staff step `0`.
- Every pitch from C4 through C6 maps to the expected consecutive step.
- Converting a staff step to a pitch round-trips throughout the configured range.
- Ledger-step calculation returns:
  - C4 → `[-2, -4]`.
  - D4/E4 and all in-staff notes → only the lines actually required.
  - G5 → no upper ledger line.
  - A5/B5 → `[10]`.
  - C6 → `[10, 12]`.
- Question generation always selects from the configured candidates.
- Question generation does not immediately repeat the exact pitch when alternatives exist.
- Duplicate letters in different octaves remain different pitch identities.
- Correct answers increment both correct and attempted counts.
- Incorrect answers increment attempted only.
- Locked questions ignore duplicate input.
- Reset clears score, unlocks the question, and cancels pending advancement.
- Future concertina schemas preserve hand, position, direction, full pitch, and duplicate valid answers.

### Functional DOM tests

- Exactly five staff lines are rendered.
- A question renders exactly one whole-note head.
- The note's SVG center matches its calculated staff step.
- Only the required ledger-line elements are rendered.
- Seven answer buttons have correct visible and accessible names.
- Each displayed keyboard shortcut activates the same answer as its button.
- Correct feedback includes a textual non-color cue.
- Incorrect feedback reveals the correct letter and marks the correct button.
- Feedback is exposed through a suitable live region.
- Buttons are temporarily unavailable after an answer and restored for the next question.
- Reset restores `0 / 0 correct` and prevents a stale timer from advancing again.
- The SVG has an accessible name that does not reveal the answer.

### Deterministic Chrome E2E tests

- Load the app without console errors.
- Complete one correct answer and observe the score become `1 / 1 correct`.
- Complete one incorrect answer and observe the attempted count increment with correct-answer feedback.
- Answer a sequence using only the keyboard.
- Reset during the feedback delay and confirm the reset state remains authoritative.
- Run with a controlled sequence of pitches and confirm no immediate exact repeat.
- Exercise the smallest supported phone viewport without horizontal page overflow.

E2E tests must use controlled questions and clocks rather than retry loops or arbitrary sleeps. They should close their own browser context and leave no server process running.

## Planned manual visual harness

Create a development-only browser page when visual testing is implemented. It should render named, deterministic scenarios rather than random questions and must not ship as part of the learner-facing navigation.

At minimum, show:

- Every pitch from C4 through C6 in order.
- C4 and C6 side by side or in adjacent cases to compare vertical clearance.
- Notes on lines and spaces.
- Zero-, one-, and two-ledger-line cases above and below the staff.
- Default, correct, incorrect, disabled, hover, and keyboard-focus button states.
- Desktop and phone-width answer layouts.
- Long or enlarged text at relevant zoom levels.

Each scenario should state what the tester must inspect and provide a checkbox or pass/fail control. The harness may persist results for the current session and produce a copyable summary. It should not attempt to decide subjective visual correctness itself.

## Phase 1 manual release checklist

### Test record

- Date:
- Tester:
- Chrome version:
- Operating system/device:
- Desktop viewport:
- Phone viewport or device:
- Edge version, if checked:

### Startup and basic behavior

- [ ] Opening `index.html` directly in Chrome shows the app without an error page.
- [ ] No errors appear in the Chrome developer console.
- [ ] The initial score is `0 / 0 correct`.
- [ ] One whole note appears immediately.
- [ ] The note changes shortly after an answer.
- [ ] The exact same pitch does not appear twice consecutively during a reasonable sample.

### Staff and notation

- [ ] Exactly five straight, evenly spaced staff lines are visible.
- [ ] The treble clef is recognizable, aligned with the staff, and not clipped.
- [ ] Every pitch C4–C6 sits precisely on its intended line or space.
- [ ] C4 displays two lower ledger lines and is not clipped.
- [ ] C6 displays two upper ledger lines and is not clipped.
- [ ] C4 and C6 have approximately balanced clearance inside the music card.
- [ ] One-ledger-line notes render only one ledger line.
- [ ] In-staff notes render no ledger lines.
- [ ] Ledger segments pass cleanly through the note head and are not excessively long.
- [ ] The whole-note head remains readable at desktop and phone widths.

Because questions are currently random, repeat play until all notes have appeared. The future visual harness will make this deterministic.

### Answers and scoring

- [ ] Buttons C through B are present, ordered, legible, and easy to target.
- [ ] A correct answer displays the word `Correct!` and increments both score numbers.
- [ ] An incorrect answer names the correct note, highlights it, and increments attempted only.
- [ ] Correct and incorrect status can be understood without relying only on color.
- [ ] Repeated clicks or keypresses during feedback count only once.
- [ ] Input becomes available again for the next question.
- [ ] Reset returns the score to `0 / 0 correct` and starts a clean question.
- [ ] Reset during feedback does not allow the old delayed transition to fire afterward.

### Keyboard and focus

- [ ] Every shortcut printed on an answer button selects that same letter.
- [ ] Holding a shortcut does not submit repeated answers.
- [ ] Modified shortcuts such as `Ctrl+C` do not answer a question.
- [ ] All buttons and Reset are reachable using `Tab` and `Shift+Tab`.
- [ ] Keyboard focus is clearly visible on every interactive control.
- [ ] Pressing `Enter` or `Space` activates a focused button normally.

### Responsive and touch layout

- [ ] At a representative desktop width, the entire exercise is balanced and readable.
- [ ] At 320 CSS pixels wide, there is no horizontal page scrolling.
- [ ] At a representative Android phone width, buttons do not overlap or clip.
- [ ] Touch targets are comfortably large and have adequate spacing.
- [ ] Rotating between portrait and landscape does not leave broken layout state.
- [ ] At 200% browser zoom, core controls and feedback remain operable.

### Accessibility

- [ ] The page has a meaningful title and heading.
- [ ] A screen reader announces answer buttons by note name.
- [ ] A screen reader announces correct and incorrect feedback at an appropriate time.
- [ ] The score and Reset control have understandable accessible names.
- [ ] The staff graphic has a useful accessible name without exposing the answer.
- [ ] Text and controls have adequate contrast in their default and feedback states.
- [ ] Disabled state and feedback do not trap or unexpectedly discard focus.

### Best-effort Edge check

- [ ] The application loads and the treble-clef glyph renders correctly in current Edge.
- [ ] Core click, keyboard, feedback, reset, and responsive behaviors match Chrome.

## Phase-based additions

Extend—not replace—this plan as phases are implemented:

1. **Phase 2:** range filters, note filters, timing with fake clocks, streak/statistics invariants, and preference combinations.
2. **Phase 3:** accidental rendering, spelling versus pitch identity, enharmonic cases, and configuration exclusions.
3. **Phase 4:** layout-schema validation, Garvey TINA C/G fixture accuracy, hand/position rendering, and layout switching.
4. **Phase 5:** button-answer matching, push/pull direction, and multiple valid physical answers.
5. **Phase 6:** sequence state, transitions between learning stages, and progress through combined drills.
6. **Phase 7:** installability, service-worker updates, offline cold start, storage migration, and recovery from corrupt data.
7. **Phase 8:** published-path asset loading, GitHub Pages smoke tests, caching behavior, and release rollback instructions.

## Exit criteria

A release candidate is acceptable when:

- All relevant automated tests pass deterministically.
- The applicable manual checklist is complete in required Chrome environments.
- Any skipped check is recorded with a reason and risk assessment.
- There are no known critical accessibility, data-loss, scoring, or notation-correctness defects.
- Documentation describes the released behavior rather than intended but absent behavior.

