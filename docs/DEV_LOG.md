# Sentinel — Development Log

This log is the running history of decisions and changes made to the
Sentinel project (firmware, hardware, tooling, and documentation
structure). Every task that changes the project must append an entry here.

## Format

Each entry should include:

- **Date**
- **Summary** — what changed.
- **Rationale** — why it changed.
- **Follow-ups** — open questions or next steps, if any.

---

## 2026-08-23 — Initial repository and environment setup

**Summary:**
- Added `.cursorrules` at the repository root, encoding the project's
  operating rules for AI agent sessions: read `docs/` and `docs/DEV_LOG.md`
  before acting, log every change here, ask before resolving ambiguity, and
  respect the `docs/` vs `docs/thesis/` documentation split.
- Initialized the PlatformIO project for an ESP32 target via
  `pio project init --board esp32dev`, producing `platformio.ini`
  (`platform = espressif32`, `board = esp32dev`, `framework = arduino`) and
  the standard `src/`, `include/`, `lib/`, `test/` folders.
- Added a minimal `src/main.cpp` stub (`setup()` starts `Serial` at
  115200 baud, `loop()` empty) as a placeholder — no sensor/motor logic
  yet.
- Added a `.gitignore` covering PlatformIO build artifacts (`.pio/`) and
  common editor/build noise.
- Created the documentation structure: `docs/` for technical
  specs/pinouts/datasheets, and `docs/thesis/` reserved exclusively for
  the LaTeX Master's Thesis documents.
- Wrote the first thesis document, `docs/thesis/01_Project_Proposal.tex`,
  covering the project proposal (introduction, objectives, justification,
  scope, requirements, expected contributions).

**Rationale:**
Establish a clean, documented starting point for the Sentinel Master's
Thesis project (NTNU Trondheim, supervisor Amund Skavhaug) before any
sensor/actuator/control code is written, so that all subsequent design
decisions are traceable.

**Follow-ups:**
- No hardware pinout or wiring decisions have been made yet (encoder, load
  cell, IMU, motor driver) — to be documented in `docs/` once decided.
- No git repository has been initialized yet in this workspace.
- `main.cpp` contains no real application logic yet.

---

## 2026-08-23 — Git repository initialized; thesis proposal compiled to PDF

**Summary:**
- Ran `git init` in the repository root. Local git identity was already
  configured globally (`Pol-M` / `polmfillat@gmail.com`); no config was
  changed. No commit has been made yet — all files are currently untracked,
  pending the user's decision on what/when to commit.
- Compiled `docs/thesis/01_Project_Proposal.tex` to PDF using the
  locally installed MiKTeX `pdflatex` (`pdflatex -interaction=nonstopmode
  -output-directory=docs/thesis docs/thesis/01_Project_Proposal.tex`).
  Produced `docs/thesis/01_Project_Proposal.pdf` (4 pages). Two minor
  "Overfull \hbox" warnings were reported (a few points of line overflow
  in the Requirements list items) — cosmetic only, not build-breaking.
- Extended `.gitignore` with LaTeX intermediate build artifacts
  (`*.aux`, `*.log`, `*.out`, `*.toc`, `*.lof`, `*.lot`, `*.fls`,
  `*.fdb_latexmk`, `*.synctex.gz`, `*.bbl`, `*.blg`) so only the source
  `.tex` and the compiled `.pdf` are tracked, not the compiler scratch
  files.

**Rationale:**
Enable version control for the project and provide a distributable PDF of
the first thesis chapter without polluting the repository with LaTeX
compiler intermediate files.

**Follow-ups:**
- No commit has been made yet; awaiting user confirmation on commit
  scope/message.
- The `CAD/` folder contains a large binary `.zip` (~6 MB) and SolidWorks
  files — worth revisiting whether these should be tracked directly, kept
  out of git, or handled via Git LFS once the repository grows.

---

## 2026-08-25 — Petzl GriGri reference image added to project proposal

**Summary:**
- Added a new `docs/thesis/figures/` asset folder and placed a Petzl
  GriGri CAD reference render there
  (`docs/thesis/figures/petzl_grigri_reference.png`, supplied by the
  user).
- Updated `docs/thesis/01_Project_Proposal.tex` (Introduction section) to
  include this image as a numbered figure (`fig:petzl_grigri`), with a
  paragraph explaining that Sentinel's rope-gripping mechanism will use
  the GriGri's camming/pinching mechanical layout as an initial design
  reference/baseline, to be extended with motor actuation for autonomous
  slack management and braking.
- Recompiled the PDF (two `pdflatex` passes, to resolve the figure
  cross-reference). Output is now 5 pages
  (`docs/thesis/01_Project_Proposal.pdf`), no warnings besides the two
  pre-existing cosmetic overfull-hbox line-wrap warnings.

**Rationale:**
Document the mechanical design inspiration explicitly in the proposal, as
requested, so the thesis proposal makes clear which parts of the design
are novel (sensing, automation, motorized control) versus adapted from an
existing proven mechanism (the GriGri's braking cam).

**Follow-ups:**
- Confirm with supervisor whether direct visual/mechanical reference to a
  commercial product (Petzl GriGri) requires an explicit citation/attribution
  in the final thesis (currently just captioned informally).
- No commit has been made yet.

---

## 2026-08-25 — Fixed figure placement drifting past following paragraph

**Summary:**
- The Petzl GriGri figure (placed with `[h!]`) was being floated by LaTeX
  to the top of the next page because it didn't fit in the remaining space
  at that point in the text — the compiler log showed `` `!h' float
  specifier changed to `!ht' ``. This pushed the figure visually after the
  next paragraph's text instead of between the two paragraphs as intended
  in the source.
- Added the `float` package and changed the figure environment from
  `\begin{figure}[h!]` to `\begin{figure}[H]` in
  `docs/thesis/01_Project_Proposal.tex`, forcing strict "print exactly
  here" placement.
- Recompiled: no more float-specifier warning; the figure now renders
  immediately after the "...manage slack." paragraph and before the
  "Sentinel proposes a fully automated..." paragraph, matching source
  order.

**Rationale:**
Default LaTeX float placement (`h`, `t`, `b`, `p`) is only a suggestion and
LaTeX will reorder floats relative to surrounding text when they don't
fit; for a short, sequential document like this proposal, exact
in-place figure ordering is more important than optimal page-fill
heuristics.

**Follow-ups:**
- No commit has been made yet.

---

## 2026-08-27 (3) — Phase 1 sketches combined into side-by-side subfigures

**Summary:**
- Merged the two separate Phase 1 sketch figures in
  `chapters/03_technical_design.tex` into a single `figure` environment
  with two `subfigure` blocks (idle/annotated view and actuated view),
  side by side at `0.48\textwidth` each. One main caption
  (`fig:sentinel_v1_sketches`) plus subcaptions (a) and (b) for each
  image (`fig:sentinel_v1`, `fig:sentinel_v1_actuant`).
- Updated the section intro text to reference the combined figure and
  both subfigures explicitly.
- Recompiled successfully (two `pdflatex` passes).

**Rationale:**
Save vertical space and present the two related Phase 1 sketches as a
single logical figure, following standard academic figure/subfigure
convention (already supported by `subcaption` in `ntnuthesis`).

**Follow-ups:**
- No commit has been made yet.

---

## 2026-08-27 (2) — Figure 1 caption fixed, Phase 1 design sketch added, project name origin documented

**Summary:**
- Fixed the caption of Figure 1 (`fig:petzl_grigri`) in
  `chapters/01_introduction.tex` to explicitly state that it is the Petzl
  GriGri, with the CAD model obtained from GrabCAD, cited with the
  numbered bibliography reference (`Petzl GriGri mechanical assembly. CAD
  model obtained from the GrabCAD model repository~\cite{...}`).
- Added a new `\section{Conceptual Design Sketch (Phase 1)}` to
  `chapters/03_technical_design.tex`, including the user-provided Phase 1
  hand sketch (`figures/Sentinel v1 actuant.jpg`) with an explanation of
  each function it depicts: rope-acceleration encoder, a load pin in the
  pinching lever's pivot axle (measuring climbing-side rope tension to
  determine when the brake can safely be released, tying back to
  \cref{sec:fr-post-fall}), a fail-safe spring (tying back to
  \cref{sec:fr-failsafe}), an actuation servo, cover axles and a
  carabiner hole, an electronics accommodation volume, and a
  position-sensing potentiometer in the pinching mechanism. Confirmed
  `\includegraphics` works correctly with the space in the source
  filename (no renaming needed).
- Added a short paragraph at the start of `chapters/01_introduction.tex`
  (before the Background section) explaining the origin of the project
  name: derived from the Old Italian word *sentinella* (a
  person/thing tasked with watching over, protecting, and alerting
  others of danger), framed as exactly what the device does for a
  climber.
- Recompiled the full document twice; no errors, no undefined
  references, only the same pre-existing cosmetic warnings.

**Rationale:**
Make the GrabCAD figure's provenance unambiguous, document the Phase 1
mechanical concept sketch and its functional rationale while the
reasoning is still fresh, and give the reader context for the project's
name early in the Introduction.

**Follow-ups:**
- The second sketch the user added (`figures/Sentinel v1.jpg`) has not
  yet been used anywhere in the report — revisit if/when relevant.
- No commit has been made yet.

---

## 2026-08-27 — Thesis Introduction rewritten (GriGri rationale) and remaining chapters scaffolded

**Summary:**
- Rewrote the GrabCAD/Petzl GriGri paragraph in `chapters/01_introduction.tex`
  (Background section) to explain the actual rationale: the Petzl GriGri
  was chosen as a mechanical reference specifically because it already
  has a mechanism with moving parts (a camming/pinching lever) that is
  plausible to turn into a mechatronic system, and a **free** 3D CAD
  model of it was sourced from GrabCAD to study its layout. Added the
  GriGri reference photo as a numbered, captioned figure
  (`figures/petzl_grigri_reference.png`, copied in from the short
  proposal's figures folder) right after this paragraph, using `[H]`
  strict placement (added `\usepackage{float}` to the main file) to avoid
  the same float-drift issue fixed earlier in the short proposal.
- Added `\section{Non-Functional Requirements}` to
  `chapters/02_functional_requirements.tex` with three initial candidate
  non-functional requirements: reliability/safety-critical performance
  (bounded worst-case reaction latency, fail-safe degradation), real-time
  responsiveness (firm deadlines for sensing/actuation), and usability/
  ergonomics (no extra training vs. conventional belay devices, clear
  feedback). Flagged as non-exhaustive/expected to grow.
- Created all remaining chapters from the NTNU thesis-template's
  "Development Project" structure as empty placeholders (heading + a
  `%` TODO comment only, no body text), added new chapter files:
  - `chapters/03_technical_design.tex`
  - `chapters/04_development_process.tex`
  - `chapters/05_implementation.tex`
  - `chapters/06_deployment.tex`
  - `chapters/07_testing_and_user_feedback.tex`
  - `chapters/08_discussion.tex`
  - `chapters/09_conclusion.tex`
  (Bibliography already existed as the unnumbered closing chapter via
  `\printbibliography`.) `\input`s for all seven were added to
  `MasterThesis_PolMasFillat.tex` between the Functional Requirements
  chapter and the Bibliography.
- Updated the "Report Structure" section in the Introduction to describe
  this actual chapter list (Technical Design, Development Process,
  Implementation, Deployment, Testing and User Feedback, Discussion,
  Conclusion), noting that all chapters beyond Introduction and
  Requirements are currently empty placeholders.
- Fixed an unrelated pre-existing typo in the Abstract ("theregulatory"
  -> "the regulatory").
- Recompiled the full document (`pdflatex` -> `biber` -> `pdflatex` x2):
  now 31 pages, no errors (only pre-existing cosmetic overfull-hbox
  warnings). Table of contents verified to match the requested chapter
  structure exactly.

**Rationale:**
Align the Background section's wording with the actual design reasoning
(GriGri chosen for its moving-parts mechanism being mechatronic-friendly,
not just "a commercial device"), and scaffold the full thesis chapter
structure now so that future work sessions can fill in one chapter at a
time without needing to restructure the document later.

**Follow-ups:**
- All seven new chapters are empty and need to be written as the project
  progresses (mechanical/electrical/software design, development process,
  implementation details, deployment, testing, discussion, conclusion).
- Verify/update the GrabCAD bibliography entry's author and date fields
  (still unconfirmed, see earlier entry).
- No commit has been made yet.

---

## 2026-08-25 — Long-form Master's Thesis document established

**Summary:**
- Created a new, scalable long-form thesis document, separate from the
  short project proposal (`01_Project_Proposal.tex`), in a new subfolder:
  `docs/thesis/Master Thesis/`.
- Two decisions were explicitly confirmed with the user before creating
  files (per the "ask before acting on ambiguity" rule), since the
  request itself contained conflicting/optional guidance:
  1. **File location**: `docs/thesis/Master Thesis/MasterThesis_PolMasFillat.tex`
     (subfolder), rather than directly in `docs/thesis/`.
  2. **Document class**: the community `ntnuthesis` LaTeX class (CC-BY
     CoPCSE@NTNU, based on the standard `report` class), rather than
     plain `report` or `book`. This is **not an official NTNU
     requirement** — it is a widely used, unofficial community template
     (see `https://github.com/COPCSE-NTNU/thesis-NTNU`). The official NTNU
     cover page (with university logo), if required for the final
     hand-in, should be obtained from Innsida and swapped in for the
     custom title page used here.
- Vendored `ntnuthesis.cls` directly into the project
  (`docs/thesis/Master Thesis/ntnuthesis.cls`), downloaded from the
  CoPCSE-NTNU GitHub repository, so the thesis is self-contained and does
  not depend on Overleaf.
- Structured the thesis as a main file
  (`MasterThesis_PolMasFillat.tex`) that `\input`s per-chapter files from
  a `chapters/` subfolder, so future chapters can be added incrementally
  without bloating a single file:
  - `chapters/01_introduction.tex` — `\chapter{Introduction}` with the
    required sections: Background, Project Area, Narrowing, Principal
    Objectives, Secondary Objectives, Research Questions, Limitations,
    Contributions, Report Structure, and Report Tools (with AI Tools,
    CAD Tools, LaTeX/Documentation Tools, and Software Development
    Environments as subsections).
  - `chapters/02_functional_requirements.tex` — `\chapter{Functional
    Requirements}` covering: Climbing Mechanics & Slack Management,
    Post-Fall State Detection, Operational Modes & Remote Control,
    Battery Management & Safety Protocols, and the critical Fail-Safe
    Mechanical Override (automatic lock on power loss + manual override
    lever).
- Per the request, all hardware is described functionally throughout
  (e.g. "embedded microcontroller", "rope-movement and tension sensing",
  "motor-driven mechanism", "wireless remote control") — no specific
  chip/board/sensor models are named. Component selection is deferred to
  future dedicated chapters.
- Added `docs/thesis/Master Thesis/references.bib` with a `biblatex`
  entry (`grabcad_petzl_grigri`) citing the Petzl GriGri CAD model from
  GrabCAD (`https://grabcad.com/library/petzl_grigri_dummy-1`), cited in
  the Introduction's Background section where the mechanical design
  reference is introduced. **Note:** the GrabCAD page could not be
  fetched to verify the uploader's name/upload date (blocked by the
  site), so the `year` field was intentionally omitted and a `note` flags
  this entry as needing verification — please check the page and update
  the entry.
- Compiled successfully end-to-end: `pdflatex` → `biber` → `pdflatex` ×2,
  producing `MasterThesis_PolMasFillat.pdf` (15 pages). No errors; only
  cosmetic overfull-hbox line-wrap warnings and harmless notices from
  unused class features (`gnuplottex` shell-escape, `datatool` dialect
  support) remain.
- Extended `.gitignore` with `*.bcf` and `*.run.xml` (additional
  biber/biblatex intermediate files produced by this document).

**Rationale:**
Separate the short, already-complete project proposal from the
long-form thesis report that will grow throughout the semester, using a
modular chapter structure and a class well-suited to long academic
documents (chapter-level structuring, bibliography support, custom
headers). Abstracting hardware descriptions keeps the Introduction and
Functional Requirements chapters valid even as concrete component
choices change during the design phase.

**Follow-ups:**
- Verify/update the GrabCAD bibliography entry's author and date fields.
- Confirm with the supervisor whether the official NTNU title page
  (from Innsida) is required, and if so replace the custom title page in
  `MasterThesis_PolMasFillat.tex` with it before any hand-in.
- Future chapters (state of the art / literature review, regulatory and
  safety analysis, system design, software architecture, testing and
  results, discussion, conclusion) should be added as new files under
  `chapters/` and `\input` from the main file, with each addition logged
  here.
- No commit has been made yet.

---

## 2026-08-27 (3) — Pushed to GitHub (`Pol-M/Sentinel`)

**Summary:**
- Extended `.gitignore`: added common OS/editor noise (`.DS_Store`,
  `Thumbs.db`, `*.swp`), excluded all thesis figure asset folders
  (`docs/**/figures/`) from version control (the `.tex` sources and
  compiled PDFs remain tracked), and excluded the `CAD/` folder entirely
  (per user decision — it holds ~12 MB of third-party Petzl GriGri
  SolidWorks reference files/archives, kept local-only rather than
  versioned; this resolves the open question flagged in the
  2026-08-23 entry).
- Added a simple root `README.md` describing the project, repository
  layout, firmware build instructions (PlatformIO), and current status.
- Added a `origin` remote pointing at the user-created GitHub repository
  `https://github.com/Pol-M/Sentinel.git`.
- Made the first commit (`093c6b2`, root commit) containing the firmware
  skeleton, `docs/` (thesis `.tex` sources + compiled PDFs, excluding
  figures), `.cursorrules`, `.gitignore`, and `README.md`, then pushed
  `master` to `origin` with upstream tracking set up.

**Rationale:**
Get the project under remote version control on GitHub as requested,
while keeping the repository lean by excluding large binary assets
(thesis figure images, third-party CAD reference files) that don't need
version history, per explicit user confirmation on the CAD folder.

**Follow-ups:**
- Figures and CAD files exist only locally now — consider a backup
  strategy for them outside of git if not already covered elsewhere.
- Verify/update the GrabCAD bibliography entry's author and date fields
  (still open from earlier).
- Confirm with the supervisor whether the official NTNU title page is
  required for the final hand-in (still open from earlier).
- This DEV_LOG entry itself was written locally but not committed/pushed
  by the agent — see rule change below; the user commits/pushes it
  themselves.

---

## 2026-08-27 (4) — Agent rules: no git commits/pushes, no PDF compiles by the agent

**Summary:**
- Added two new rules to `.cursorrules`:
  - **Rule 5**: the agent must not run `git add` / `git commit` / `git
    push` — the user handles all staging, committing, and pushing
    themselves. Read-only git commands (`status`, `diff`, `log`, etc.)
    are still fine.
  - **Rule 6**: the agent must not compile the LaTeX thesis to PDF
    (`pdflatex`/`biber`/etc.) unless strictly necessary to verify a
    change actually compiles — the user exports PDFs themselves
    otherwise.

**Rationale:**
The user wants full manual control over version-control history and over
when/how the thesis PDF is (re)generated, rather than having the agent do
either automatically as a side effect of other tasks.

**Follow-ups:**
- None.
