<!--
Sync Impact Report
==================
Version change: (template placeholders) → 1.0.0
Modified principles: N/A (initial ratification)
Added sections:
  - Core Principles (5 principles)
  - Additional Constraints
  - Development Workflow
  - Governance
Removed sections: None (placeholders replaced)
Templates requiring updates:
  - .specify/templates/plan-template.md ✅ updated (Technical Context + Constitution Check gates)
  - .specify/templates/tasks-template.md ✅ updated (path conventions for single-file SPA)
  - .specify/templates/spec-template.md ✅ no changes required
  - .specify/templates/checklist-template.md ✅ no changes required
Follow-up TODOs: None
-->

# todolist Constitution

## Core Principles

### I. Vanilla Web Stack (NON-NEGOTIABLE)

All application code MUST be plain HTML, CSS, and JavaScript with no frameworks,
transpilers, bundlers, or package managers. The app MUST remain runnable by
opening `index.html` directly in a browser with zero build step. Third-party
assets are limited to CDN-hosted fonts and icons already in use (Google Fonts,
Font Awesome). New dependencies MUST NOT be added without a constitution
amendment that documents why vanilla JS is insufficient.

**Rationale**: The project is a deliberately zero-toolchain demo; complexity
from build tooling would contradict its purpose and deployment model.

### II. Single-Page, Mobile-First UI

The application MUST stay a single-page app contained primarily in `index.html`.
All new UI MUST work on mobile viewports (≤768px) and desktop without separate
code paths unless the existing pattern already uses them (e.g., mobile column
tabs, arrow-button fallbacks for drag-and-drop). Layout MUST preserve the
viewport-locked design: no double scrollbars on `body`, scrollable column bodies
only. Form inputs on mobile MUST use ≥16px font size to prevent iOS auto-zoom.

**Rationale**: Responsive mobile support is a stated product goal and is already
engineered into layout, tabs, and touch fallbacks.

### III. Client-Side Persistence via localStorage

All durable task data MUST persist in the browser using `localStorage` under the
key `'todolist'`. Reads and writes MUST go through the existing `loadFromStorage`
/ `saveToStorage` pattern (or direct equivalents that preserve the same key and
schema). Features MUST NOT introduce a backend, sync service, or alternate
storage layer unless explicitly approved via constitution amendment. Schema changes
MUST remain backward-compatible or include a documented migration path.

**Rationale**: Offline, per-browser persistence is the app's only data tier; no
server infrastructure exists or is planned.

### IV. Minimal, Pattern-Consistent Changes

Every change MUST be the smallest diff that satisfies the requirement. New code
MUST match existing conventions: CSS custom properties in `:root`, numbered
section comments, global functions with descriptive names, toast feedback for
user actions, confirmation modals for destructive operations, and imperative
`render()`-based DOM updates. Refactors, abstractions, or file splits MUST NOT be
introduced unless a feature cannot be implemented otherwise and the plan's
Complexity Tracking table justifies the deviation.

**Rationale**: The codebase is a cohesive single-file artifact; consistency and
 restraint keep it maintainable without a module system.

### V. Kanban Workflow Integrity

Task lifecycle MUST respect the three-column Kanban model (To Do → In Progress →
Done) and its enforced rules: tasks cannot skip In Progress to reach Done;
completion toggles are valid only in In Progress; moving to Done requires
`completed === true`; backward moves reset completion; Done tasks are read-only
(not editable, not draggable, priority locked). New features MUST NOT bypass
these rules without an explicit spec decision and constitution amendment.

**Rationale**: Workflow discipline is core product behavior encoded throughout
move, edit, and context-menu logic—not incidental UI detail.

## Additional Constraints

- **Single-file source**: Application logic, styles, and markup live in
  `index.html` at the repository root unless a future amendment authorizes a split.
- **Design system**: Use existing CSS tokens (`--primary`, `--text-*`,
  `--priority-*`, shadows, transitions). Do not hardcode one-off colors.
- **Task validation**: Title 3–40 characters; description ≤150 characters;
  priority ∈ {low, medium, high}. Validation MUST surface errors via toast, not
  silent failure.
- **User feedback**: Success, warning, error, and info outcomes MUST use the
  existing toast system; bulk deletes MUST use the confirmation modal.
- **No authentication or multi-user**: The app is single-user, client-only.
- **Testing**: Manual browser verification is the default; automated test
  infrastructure is out of scope unless explicitly requested in a feature spec.

## Development Workflow

1. Read this constitution before planning or implementing any feature.
2. Run `/speckit-plan` and complete the **Constitution Check** gates in
   `plan.md` before design work proceeds.
3. Implement changes in `index.html` following existing section structure
   (CSS tokens → HTML regions → JS modules by comment blocks).
4. Verify on at least one desktop and one mobile viewport (or DevTools
   responsive mode) before marking work complete.
5. Persist state changes through `saveToStorage()` immediately after mutating
   `state.tasks` or persisted filter fields.
6. Document any constitution violation in the plan's Complexity Tracking table
   with rationale and rejected simpler alternatives.

## Governance

This constitution supersedes ad-hoc decisions in specs, plans, and PRs when they
conflict. Amendments require: (1) documented rationale, (2) version bump per
semantic versioning below, (3) sync of affected templates under
`.specify/templates/`, and (4) update of `LAST_AMENDED_DATE`.

**Versioning policy**:

- **MAJOR**: Removing or redefining a core principle (e.g., adopting a
  framework or backend).
- **MINOR**: Adding a principle or materially expanding constraints.
- **PATCH**: Wording clarifications with no semantic change.

All feature plans and reviews MUST verify compliance with Core Principles I–V.
Unjustified complexity MUST be rejected or documented in Complexity Tracking.

**Version**: 1.0.0 | **Ratified**: 2026-06-01 | **Last Amended**: 2026-06-01
