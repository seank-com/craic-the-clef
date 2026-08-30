# Architecture and Engineering Standards

## Purpose

Craic the Clef begins as a deliberately small sight-reading exercise and is expected to grow into an offline-capable notation and concertina trainer. The architecture must preserve the speed and clarity of the MVP while making later musical concepts explicit and testable.

This document describes the target direction. It does not claim that the current single-file application has already been reorganized.

## Architectural principles

### Domain logic before interface logic

Musical facts should be represented independently of SVG, buttons, timing, or browser state. A pitch is not a pixel coordinate, a note-name answer, or a concertina button.

Core transformations should be pure functions wherever possible:

- Pitch ↔ diatonic number.
- Pitch ↔ staff step.
- Staff step → ledger-line steps.
- Exercise configuration → candidate pitches.
- Previous question plus random source → next question.
- Answer plus state → next score/state.
- Instrument layout plus pitch/direction → valid physical buttons.

Pure domain code is the primary unit-test boundary.

### Explicit layers

The design separates seven concerns:

1. **Music model:** pitch identity, octave, note letter, accidental, and eventually written spelling.
2. **Staff model:** clef, range, diatonic positions, and ledger-line rules.
3. **Notation view:** SVG elements and conversion of staff geometry into coordinates.
4. **Exercise engine:** candidate selection, avoidance of immediate repeats, validity rules, and future difficulty policies.
5. **Input adapters:** pointer, keyboard, and eventually concertina-diagram interaction.
6. **Session state:** current question, locking/timing, score, streaks, statistics, and persistence boundaries.
7. **Instrument layouts:** declarative concertina definitions and queries over their button/pitch data.

Dependencies should point inward toward domain concepts. In particular, the music model and instrument data must not import or know about the DOM or SVG.

### Data-driven instruments

Concertina layouts are data, not application branches. A future shape may resemble:

```js
{
  id: "garvey-tina-cg",
  name: "Garvey TINA C/G",
  hands: {
    left: [
      {
        id: "left-outer-1",
        row: 0,
        column: 0,
        push: { letter: "C", accidental: 0, octave: 4 },
        pull: { letter: "D", accidental: 0, octave: 4 }
      }
    ],
    right: []
  }
}
```

The final schema should be derived from real layout requirements and validated by tests. Physical position, playing direction, and octave-specific pitch are mandatory concepts. Duplicate pitches must remain representable because more than one physical answer may be valid.

### Pitch identity and spelling

The current `{ letter, octave, id }` representation is enough for naturals. Before Phase 3, evolve it so sounding pitch and displayed spelling are not accidentally conflated. Enharmonic spellings must be possible without forcing renderer-specific concepts into the pitch model.

## Evolution from the single file

Keep `index.html` self-contained while Phase 1 remains genuinely simple. Split code when a behavioral change benefits from an independently testable boundary—not merely to create folders.

The expected native-module shape is:

```text
index.html
src/
├── music/
│   ├── pitch.js
│   └── staff-position.js
├── notation/
│   └── svg-renderer.js
├── exercises/
│   └── note-identification.js
├── instruments/
│   ├── layout-schema.js
│   └── layouts/
├── state/
│   └── session.js
├── input/
│   └── keyboard.js
└── main.js
tests/
├── unit/
├── functional/
└── e2e/
```

This is a direction, not a requirement to create empty files. Use browser-native ES modules. Once modules are introduced, local development should use a small static server because module loading from `file://` is restricted. Avoid a framework and bundler until a concrete requirement justifies them.

## SVG notation

Notation is assembled from reusable primitives: staff line, treble clef, whole-note head, and ledger-line segment.

The current coordinate model is intentionally explicit:

- E4, the bottom treble-staff line, is step `0`.
- Each diatonic pitch movement changes the step by one.
- Lines occupy even steps and spaces occupy odd steps.
- SVG `y = bottomY - (step × halfSpace)`.
- Staff lines are steps `0`, `2`, `4`, `6`, and `8`.
- Below-staff ledger lines are the necessary even steps at or below `-2`.
- Above-staff ledger lines are the necessary even steps at or above `10`.

The range is C4 (step `-4`) through C6 (step `12`). Geometry constants apply to the entire notation system so the staff can be repositioned without breaking note or ledger alignment. Keep enough view-box clearance for the full note head and stroke at both extremes.

Do not hard-code ledger lines per named pitch. Do not use raster notation images. If notation needs eventually exceed straightforward SVG, assess a notation library at that time rather than pre-emptively adopting one.

## State and time

Keep authoritative session state in one explicit object or reducer-like module. UI classes and text are projections of state, not alternate sources of truth.

Time-based behavior must be injectable or controllable in tests. Random selection must accept an injectable random source or deterministic candidate selector after modularization. Tests must never depend on chance or real-time sleeps.

Prevent duplicate submissions while feedback is displayed. Reset must cancel pending transitions so an old timer cannot change a newly reset session.

## Coding style

- Prefer clear domain names over abbreviations.
- Keep functions small and focused on one reason to change.
- Prefer immutable return values for domain operations.
- Use data tables and mappings instead of repeated conditionals.
- Validate data at system boundaries and fail with useful messages.
- Avoid premature generic abstractions; extract only demonstrated common behavior.
- Keep DOM queries and mutations in view/adaptor code.
- Use comments to explain musical rules and non-obvious trade-offs, not syntax.
- Preserve the established formatting style until an automated formatter is deliberately adopted.
- Avoid hidden global state. Explicitly pass dependencies such as randomness, clocks, storage, and layouts.

## Test-driven development

All testable behavior follows strict red–green–refactor:

1. State one observable behavior.
2. Add the smallest test expressing it.
3. Run the test and confirm it fails for the expected reason.
4. Implement the smallest production change that passes it.
5. Run the focused test and then all deterministic tests.
6. Refactor names, boundaries, and duplication while tests remain green.
7. Update documentation and the test plan when contracts change.

A test that never demonstrated a meaningful failure does not provide the same evidence. When correcting a behavioral defect, preserve the reproducing test as regression protection.

Use layered tests:

- **Unit tests:** pure pitch, staff, ledger, exercise, score, and layout functions. These should dominate the suite.
- **Functional DOM tests:** rendering structure, input mapping, state projection, feedback, reset, and accessibility attributes.
- **Deterministic E2E tests:** critical flows in Chrome when a real browser adds confidence that lower layers cannot.
- **Manual visual tests:** clipping, balance, legibility, touch layout, and visual feedback. Use the browser harness/checklist described in `test-plan.md`; do not use fragile pixel snapshots by default.

Select the smallest low-maintenance tooling that can cover these layers when tests are implemented. Prefer platform capabilities such as the Node test runner for pure modules if they meet the need. A browser automation dependency is justified only for deterministic browser behaviors worth maintaining.

## Accessibility

Accessible use is a formal quality criterion even though the product concerns a physical instrument.

- Every interaction must be possible from the keyboard.
- Focus must be visible and logical.
- Controls must have accurate accessible names and states.
- Dynamic correctness and score feedback must be announced appropriately without becoming noisy.
- Correct/incorrect status must use text or another non-color cue as well as color.
- Text, notation, and controls must meet sensible contrast requirements.
- Touch controls must remain comfortably sized at supported phone widths.
- Semantic HTML is preferred over recreated control behavior.
- Test at common zoom levels and avoid layouts that require precision pointing.

Reduced motion remains a good progressive enhancement, not a central acceptance gate. Screen-reader behavior, contrast, keyboard access, and touch usability are release concerns.

## Browser and deployment constraints

Current Chrome is the required browser. Current Edge is best-effort. Firefox and Safari compatibility are welcome but not release gates.

The app must remain deployable as static files. Use relative asset paths that work from a GitHub Pages project subpath. Do not assume a server-side API. Phase 7 may add static PWA assets and browser storage, but core drills must remain offline-capable.

## Quality gates

A production-quality change should satisfy all applicable gates:

- Requirements and scope are explicit.
- Testable behavior was developed red-first.
- Deterministic checks pass without reliance on timing or random outcomes.
- Relevant manual visual and accessibility checks are recorded.
- No new console errors or unhandled promise rejections appear.
- Chrome passes at supported desktop and phone-sized viewports.
- Edge regressions are checked when practical.
- Documentation matches actual behavior.
- New dependencies, if any, have a written justification.

