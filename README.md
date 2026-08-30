# Craic the Clef

Craic the Clef is a small, fast sight-reading trainer. Its first phase teaches natural notes on the treble staff; later phases will connect written pitches to the physical buttons and push/pull directions of configurable Anglo-style concertinas.

The application currently has no dependencies, build step, server, or installation process.

## Current features

- Natural whole notes from C4 through C6.
- A responsive SVG treble staff with generated ledger lines.
- Large C, D, E, F, G, A, and B answer buttons.
- Matching C–B keyboard shortcuts, as shown on the buttons.
- Immediate feedback, correct-answer highlighting, scoring, and reset.
- No immediate repeat of the exact same pitch.
- Phone and desktop layouts.

## Run locally

The quickest option is to open `index.html` directly in Chrome:

1. Open the project folder.
2. Double-click `index.html`, or drag it into Chrome.
3. Answer by clicking/tapping a button or pressing its displayed keyboard shortcut.

No network connection is required.

### Optional local web server

Direct file opening works today. Once the code is split into native ES modules or gains a service worker, browsers will require a local HTTP server.

If Python is installed, run this from the project directory:

```powershell
python -m http.server 8000
```

Then open [http://localhost:8000](http://localhost:8000) in Chrome. Stop the server with `Ctrl+C`.

## Testing

There is not yet an automated test setup. The planned strategy is documented in [docs/architecture.md](docs/architecture.md), and the repeatable manual and automated test inventory is in [docs/test-plan.md](docs/test-plan.md).

For the current application, complete the Phase 1 manual checklist before releasing changes. At minimum, exercise correct and incorrect answers, all keyboard shortcuts, reset, the extreme notes C4 and C6, ledger lines, and desktop/phone-sized layouts in Chrome.

## Project structure

```text
.
├── index.html              Current self-contained application
├── AGENTS.md               Durable instructions for coding agents
├── README.md               Setup and project overview
└── docs/
    ├── architecture.md     Design and engineering standards
    └── test-plan.md        Automated strategy and manual checklist
```

The single-file implementation is intentional for the MVP. It will be split gradually into native ES modules as behavior grows, without adopting a framework or bundler by default.

## Product roadmap

1. Whole-note sight reading.
2. Practice controls.
3. Accidentals.
4. Data-driven concertina layouts.
5. Concertina note-location drills.
6. Combined notation and instrument fluency.
7. Progressive Web App and offline persistence.
8. Publication as a static site.

See [AGENTS.md](AGENTS.md) for the preserved scope of each phase.

## Future GitHub Pages deployment

> **Placeholder only:** do not perform these steps until the repository has been created and reviewed at home.

The intended initial deployment is the simplest GitHub Pages configuration: publish the repository root from the `main` branch.

When ready:

1. Initialize Git in this directory and make the first commit.
2. Create an empty GitHub repository and add it as the remote.
3. Push the project to the `main` branch.
4. On GitHub, open **Settings → Pages**.
5. Under **Build and deployment**, choose **Deploy from a branch**.
6. Select the `main` branch and `/ (root)`, then save.
7. Wait for GitHub to report the published URL.
8. Open that URL in Chrome and run the release checklist in `docs/test-plan.md`.

Do not add a GitHub Actions workflow unless later requirements make branch-based Pages deployment insufficient.

