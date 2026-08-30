# Craic the Clef: Agent Guide

This file is durable project context for coding agents. Read it before changing the application. Also read `README.md`, `docs/architecture.md`, and `docs/test-plan.md` when the task touches their subject matter.

## Product goal

Craic the Clef is a lightweight sight-reading trainer that will grow into an offline-capable Progressive Web App for learning both musical notation and Anglo-style concertina button mappings. It must remain quick to start, quick to play, legible on phones and desktops, and usable without a server for its core functions.

The central long-term learning path is:

> written note → note name → concertina button → push/pull

## Current state

Phase 1 is implemented in a single, dependency-free `index.html` containing HTML, CSS, and JavaScript.

Current behavior includes:

- A responsive SVG treble staff with five lines and a treble clef.
- One randomized natural whole note from C4 through C6.
- Ledger lines calculated from the note's diatonic staff position.
- Large C–B answer buttons and matching C–B keyboard shortcuts.
- Immediate correct/incorrect feedback, correct-answer highlighting, score, and reset.
- Prevention of an immediate repeat of the exact same pitch.
- Basic semantic labels and live feedback for assistive technology.

The SVG uses a `700 × 300` view box. The bottom staff line, E4, is diatonic step `0`; each step changes vertical position by half a staff space. The configured range is step `-4` through `12`, or C4 through C6. Staff geometry currently uses `bottomY = 210` and `halfSpace = 15`.

There is no package manager, build system, automated test suite, repository configuration, service worker, or deployment configuration yet.

## Non-negotiable design boundaries

Keep these concerns separate as the code is modularized:

1. Musical pitch and spelling.
2. Staff range and pitch-to-position conversion.
3. SVG notation rendering.
4. Exercise/question generation.
5. Input handling.
6. Scoring and session state.
7. Instrument and concertina layout data.

Never put concertina button positions into notation-rendering logic. Instrument layouts must be declarative data. Buttons will eventually identify hand, physical position, push pitch, and pull pitch using octave-specific pitch identities—not letter names alone.

Keep pitch identity separate from its visual spelling so accidentals and enharmonic spellings can be added correctly later.

## Engineering rules

- Use strict test-driven development for behavioral changes: add a test that fails for the intended reason, implement the smallest passing change, and then refactor while green.
- Do not write a test after implementation merely to describe code that already exists.
- For genuinely visual judgments, follow the manual visual-test workflow in `docs/test-plan.md`. Do not create brittle pixel snapshots just to claim TDD coverage.
- Fix regressions by first adding a test that reproduces them when they can be asserted deterministically.
- Prefer native browser APIs and zero dependencies. Add a dependency only when its continuing value clearly exceeds its maintenance and supply-chain cost.
- Evolve the single file into native ES modules gradually. Do not introduce a framework or bundler without a demonstrated need.
- Prefer small, cohesive modules and pure functions. Avoid large conditional blocks when a table or data structure expresses the domain more clearly.
- Keep the app capable of static hosting. Core practice must ultimately work offline.
- Preserve user changes and inspect the current file before editing. Visual constants may have been deliberately tuned by eye.
- Update relevant documentation and tests in the same change as behavior.

## Accessibility and browser support

Chrome is the required browser. Edge is best-effort. Firefox and Safari support are welcome but are not release gates.

Accessibility is a product requirement, especially:

- Full keyboard operation with visible focus.
- Accurate labels, roles, and live feedback for screen readers.
- Feedback that does not rely on color alone.
- Sufficient text and control contrast.
- Large touch targets and responsive layouts.
- Sensible document structure and focus behavior.

Reduced-motion support is courteous but is not currently a core acceptance requirement. Do not remove it without a reason.

## Roadmap

Preserve this product scope unless the user explicitly changes it.

1. **Whole-note sight reading (current):** treble clef, natural whole notes, ledger lines, C–B answers, keyboard input, score, and responsive layout.
2. **Practice controls:** selectable range, staff-only/ledger-note choices, specific notes, timed drills, streaks/statistics, and progressive difficulty.
3. **Accidentals:** sharps, flats, naturals, configurable inclusion, and correct separation of pitch from visual spelling.
4. **Concertina layouts:** declarative instrument definitions, layout selection, the Garvey TINA C/G 30-button layout, and graphical 3×5 arrangements for both hands.
5. **Concertina note-location drills:** identify physical buttons and later push/pull direction; allow multiple valid buttons for duplicate pitches.
6. **Combined fluency:** written note → note name → concertina button → push/pull, followed by note sequences.
7. **PWA:** manifest, service worker, offline operation, installability, and persistent preferences/progress.
8. **Publish:** initialize Git and a GitHub repository, then publish as a static site, likely with GitHub Pages.

Do not implement a later phase incidentally while working on an earlier one.

## Completion standard

Before calling a change complete:

- Confirm the requested behavior and its boundaries.
- Demonstrate the failing test first for testable behavior.
- Run the smallest relevant checks, then the broader deterministic suite.
- Complete relevant manual items in `docs/test-plan.md` when appearance or interaction changed.
- Check Chrome at required desktop and phone-sized viewports for user-facing changes.
- Report what changed, what was verified, and anything not verified.

