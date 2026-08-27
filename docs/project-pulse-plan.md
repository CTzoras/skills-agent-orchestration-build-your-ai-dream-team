# Project Pulse implementation plan

## Goal and constraints

Build a dependency-free static Project Pulse dashboard for contributors. The
dashboard should make active projects, owners, statuses, recent activity,
priorities, and short contributor-friendly summaries easy to scan. It should
present polished project cards and run from the VS Code **Run Project Pulse
Dashboard** configuration without opening a server directory listing.

This plan follows the existing [agent role definitions](../.github/agents/)
and the [Project Pulse brief](../.github/project-pulse-brief.md). The Planner,
Designer, Coder, and Orchestrator retain separate responsibilities and must not
stage, commit, or push changes.

## Ordered phases

### 1. Planner research and planning

**Owner:** Planner  
**File scope:** `docs/project-pulse-plan.md`

1. Research the brief, repository conventions, and agent definitions.
2. Confirm the dependency-free static-app constraint and the required
   `projects` data shape: a top-level `projects` array whose entries include
   `name`, `owner`, `status`, `recentActivity`, and `priority`.
3. Establish the file ownership, dependencies, risks, and validation checklist
   recorded in this plan.

The agreed requirements and data shape are the gate before markup or final
styling begins.

### 2. Designer guidance

**Owner:** Designer  
**File scope:** design guidance for the implementation; no overlapping
   implementation files

The Designer defines UX and information hierarchy for a dashboard that quickly
communicates project health. Responsibilities include:

- deciding the hierarchy and readable grouping of project name, owner, status,
  recent activity, priority, and contributor summary;
- specifying accessible semantics, keyboard/focus behavior, labels, and
  sufficient contrast for status and priority treatments;
- defining responsive behavior for narrow, medium, and wide viewports;
- guiding polished project-card and status-badge styling, readable spacing,
  typography, rounded corners, and visual affordances; and
- recommending deterministic hooks such as `.dashboard` and
  `.project-card` so implementation and validation remain clear.

Designer and Coder preparation may overlap only after Phase 1 requirements are
agreed and their file ownership is separate. Design guidance must be available
before the Coder finalizes styling.

### 3. Coder implementation

**Owner:** Coder  
**Files assigned:**

- `app/index.html` — semantic dashboard markup and dependency-free loading and
  rendering of project data;
- `app/styles.css` — responsive, accessible, polished layout and card/status
  presentation, including `.dashboard`, `border-radius`, and `box-shadow`;
- `app/project-data.json` — valid fixture data with a top-level `projects`
  array and the required project fields; and
- `.vscode/launch.json` — strict JSON launch configuration named **Run Project
  Pulse Dashboard**, with `cwd` set to `${workspaceFolder}/app`, serving the
  app directory, and opening `index.html`.

The Coder implements static HTML/CSS/JSON only, without a package install or
runtime dependency. Markup should expose visible `project-card` elements and
status treatments, load `project-data.json` predictably, and provide a clear
error state if data loading or parsing fails. The launch configuration must
open `index.html` from `app/`, rather than leaving learners at a directory
listing.

### 4. Orchestrator integration and validation

**Owner:** Orchestrator  
**Files reviewed:** the four Coder-assigned files and this plan

1. Review the integrated output against the brief, Designer guidance, data
   contract, and explicit file ownership.
2. Run `bash scripts/validate-exercise.sh` from the repository root.
3. Parse `app/project-data.json` and `.vscode/launch.json` as JSON; inspect
   malformed-data and missing-required-field behavior.
4. Confirm the UI contains Project Pulse, visible `project-card` elements,
   status, recentActivity, and priority content, and that CSS contains
   `.dashboard`, `border-radius`, and `box-shadow`.
5. Confirm the data includes `projects`, `name`, and `owner` fields (as well as
   `status`, `recentActivity`, and `priority`).
6. Confirm the **Run Project Pulse Dashboard** configuration serves from
   `app/` and opens `index.html`. Perform browser verification at representative
   desktop and narrow viewport sizes to catch launch, loading, accessibility,
   and responsive-layout regressions.

Integration, launch validation, and browser verification are sequential: each
depends on the completed app files and the preceding checks.

## Dependencies and parallel-work decisions

The dependency order is:

1. Requirements and the JSON data shape must be agreed before markup and data
   implementation.
2. Designer guidance must precede final CSS styling and visual polish.
3. All app files and launch configuration must exist before integrated
   validation, launch checks, and browser verification.

After Phase 1, Designer guidance and Coder preparation can proceed in parallel
only while ownership remains separate and the Coder does not finalize styling
until the guidance is available. Work is otherwise sequential where it
overlaps, consumes a prior artifact, or requires an integrated result.

## Edge cases and risks

- **Malformed JSON:** parsing must fail visibly and predictably rather than
  leaving an empty or misleading dashboard.
- **Missing required fields:** incomplete project records should be rejected,
  skipped with an informative message, or otherwise handled without broken
  cards.
- **Inaccessible contrast or semantics:** status/priority color must not be
  the sole signal; headings, labels, landmarks, focus states, and contrast
  need review.
- **Data loading failures:** show a useful error state for unavailable
  `project-data.json` (including static-server or path mistakes).
- **Directory-listing launch behavior:** verify `cwd` and the explicit
  `index.html` target so Run Project Pulse Dashboard opens the UI.
- **Responsive layout issues:** cards, badges, text, and controls must remain
  readable and usable without overflow on narrow screens.

## Validation expectations

The final handoff should report the result of `bash scripts/validate-exercise.sh`
and supplement it with:

- successful JSON parsing for `app/project-data.json` and
  `.vscode/launch.json`;
- required Project Pulse text and `styles.css` / `project-data.json`
  references;
- `project-card`, `status`, `recentActivity`, and `priority` in the rendered
  dashboard or its data/rendering paths;
- `.dashboard`, `border-radius`, and `box-shadow` in the stylesheet;
- `projects`, `name`, and `owner` fields in the JSON data; and
- confirmation that **Run Project Pulse Dashboard** opens `index.html` from
  `app/`, not a directory listing, plus browser verification of loading,
  accessibility, and responsive behavior.
