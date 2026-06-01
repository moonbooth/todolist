# Implementation Plan: Task Due Dates

**Branch**: `001-task-due-dates` | **Date**: 2026-06-01 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `specs/001-task-due-dates/spec.md`

**Note**: Single-file vanilla SPA (`index.html`). No build tools, no frameworks. All durable state in `localStorage` key `todolist`.

## Summary

Add an optional calendar due date to each task with create/edit inputs inline in the add-task row and edit modal. Display `Due Jun 15` on cards and in the view modal. Apply visual urgency: **red** left border + red label when overdue (before today, not Done), **amber** when due today. Extend the sort dropdown with soonest/latest due-date options. Persist `dueDate` on each task object inside the existing `state` blob; no schema version bump required—missing field means undated.

## Technical Context

**Language/Version**: Vanilla HTML5, CSS3, ES6+ JavaScript (no transpilation)

**Primary Dependencies**: CDN only — Google Fonts (Plus Jakarta Sans), Font Awesome 6.x

**Storage**: Browser `localStorage` (key: `todolist`); no backend

**Testing**: Manual browser verification (desktop + mobile viewport); see [quickstart.md](./quickstart.md)

**Target Platform**: Modern browsers (Chrome, Firefox, Safari, Edge; iOS Safari, Android Chrome)

**Project Type**: Single-page web app (`index.html` at repository root)

**Performance Goals**: Smooth interaction for typical personal task lists (<500 tasks); full `render()` acceptable

**Constraints**: No build tools, no frameworks, offline-capable, mobile-first responsive layout

**Scale/Scope**: Single-user, client-only; all code changes in `index.html`

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Gate | Requirement | Pre-design | Post-design |
|------|-------------|------------|-------------|
| G1 — Vanilla stack | Principle I | ✅ Pass | ✅ Pass |
| G2 — Single-page mobile UI | Principle II | ✅ Pass | ✅ Pass |
| G3 — localStorage persistence | Principle III | ✅ Pass | ✅ Pass |
| G4 — Minimal diff | Principle IV | ✅ Pass | ✅ Pass |
| G5 — Kanban rules | Principle V | ✅ Pass | ✅ Pass |

All gates pass. No Complexity Tracking entries required.

## Project Structure

### Documentation (this feature)

```text
specs/001-task-due-dates/
├── plan.md              # This file
├── research.md          # Phase 0 decisions
├── data-model.md        # Task schema extension
├── quickstart.md        # Manual verification steps
├── contracts/           # Storage + UI contracts
│   ├── localStorage-schema.md
│   └── ui-due-date.md
└── tasks.md             # Phase 2 (/speckit-tasks — not yet created)
```

### Source Code (repository root)

```text
index.html                 # All CSS, HTML, and JavaScript (sole implementation target)
README.md
LICENSE
specs/001-task-due-dates/  # Feature design artifacts
```

**Structure Decision**: Extend `index.html` only. Changes grouped by existing file sections: `:root` tokens → task-card CSS → add/edit/view HTML → JS helpers → `addNewTodo` / `saveEditedTask` / `render` sort / `createTaskCardDOM`.

## Implementation Approach

### Phase A — Data & helpers (foundation)

1. Add CSS custom properties for due-state colours (`--due-overdue-*`, `--due-today-*`) reusing existing palette values where possible.
2. Add pure functions (new JS section after date helpers):
   - `parseDueDate(str)` — validate `YYYY-MM-DD` or return null
   - `getLocalDateString(date)` — today as `YYYY-MM-DD` in local TZ
   - `getDueDateUrgency(task)` — `'none' | 'future' | 'today' | 'overdue'` (returns `'none'` for undated or Done column)
   - `formatDueDateLabel(dueDateStr)` — `Due Jun 15` / `Due Jun 15, 2027`
3. Extend `newTask` in `addNewTodo()` with `dueDate: null | string`.
4. On add/edit save, read `<input type="date">`; empty → `null`.

### Phase B — User Story 1: Set due date (P1)

| Location | Change |
|----------|--------|
| `#add-todo-main-row` `.row-right-group` | Add `<input type="date" id="todoDueDateInput">` with label after priority select |
| `#editModal` `.modal-body` | Add due date field after priority group |
| `#viewModal` `.modal-body` | Add read-only due date row (`#viewTaskDueDate`) |
| `addNewTodo()` | Read/clear due input; reset on success |
| `openEditModal()` / `saveEditedTask()` | Populate/save/clear due date |
| `openViewModal()` | Display formatted due label or "No due date" |

Mobile: ensure date inputs use `font-size: 16px` in existing `@media (max-width: 768px)` block.

### Phase C — User Story 2: Visual urgency (P2)

| Location | Change |
|----------|--------|
| CSS | `.task-card.due-overdue` / `.task-card.due-today` override `border-left-color`; `.task-due-date` label colours |
| `createTaskCardDOM()` | Add due label in header (between priority and time or below header row); apply urgency classes on card when not Done |
| Done column | Never apply `.due-overdue` / `.due-today` classes |

**Border conflict resolution**: Priority classes set default left border; urgency classes override border colour only (same 4px width). Overdue takes precedence if ever ambiguous (dates are mutually exclusive).

### Phase D — User Story 3: Sort by due date (P3)

| Location | Change |
|----------|--------|
| `#sortBySelect` | Add `due-date-asc`, `due-date-desc` options |
| `render()` sort block | Compare ISO date strings; undated tasks sort last; tie-break with `createdAt` desc |

Persisted via existing `state.sortBy` — no separate field.

### Phase E — Polish

- Existing `setInterval` timestamp refresh: optional midnight check not required (overdue flips on next `render()` or reload).
- Demo tasks: leave without `dueDate` (backward compatible).
- `loadFromStorage()`: no migration script; treat missing `dueDate` as null.

## Risk & Mitigation

| Risk | Mitigation |
|------|------------|
| Priority vs due border both use `border-left` | Urgency modifier classes override colour only |
| Mobile row crowding | Wrap `.row-right-group` items; test at 375px width |
| iOS date picker zoom | 16px font on date inputs in mobile media query |

## Complexity Tracking

> No constitution violations.

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| — | — | — |
