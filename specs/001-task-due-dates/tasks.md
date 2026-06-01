# Tasks: Task Due Dates

**Input**: Design documents from `specs/001-task-due-dates/`

**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/, quickstart.md

**Tests**: Manual browser verification only (see quickstart.md). No automated test tasks.

**Organization**: Tasks grouped by user story. All implementation targets `index.html` at repository root.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Parallelizable (skipped for this feature—all tasks edit `index.html` sequentially)
- **[Story]**: Maps to spec user stories US1–US3

## Path Conventions

- Application code: `index.html` (CSS in `<style>`, markup in `<body>`, logic in `<script>`)
- Feature docs: `specs/001-task-due-dates/`

---

## Phase 1: Setup

**Purpose**: Confirm environment and design context before editing code

- [x] T001 Verify branch `001-task-due-dates` and review `specs/001-task-due-dates/plan.md`, `spec.md`, and `data-model.md`

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Shared CSS tokens, date helpers, and task schema extension required by all user stories

**⚠️ CRITICAL**: No user story work until this phase is complete

- [x] T002 Add `--due-overdue-border`, `--due-overdue-text`, `--due-today-border`, and `--due-today-text` tokens to `:root` in `index.html`
- [x] T003 Add helper functions `getLocalDateString`, `parseDueDate`, `getDueDateUrgency`, and `formatDueDateLabel` after existing date helpers in `index.html`
- [x] T004 Extend `newTask` in `addNewTodo()` with `dueDate: null` (ISO `YYYY-MM-DD` when set) in `index.html`

**Checkpoint**: Helpers and schema ready—user story phases can begin

---

## Phase 3: User Story 1 - Set an Optional Due Date (Priority: P1) 🎯 MVP

**Goal**: Users can set, edit, clear, and view optional due dates; values persist in `localStorage`

**Independent Test**: Create a dated task and an undated task; refresh browser; edit due date; confirm Done tasks remain read-only (spec US1 scenarios)

### Implementation for User Story 1

- [x] T005 [US1] Add `#todoDueDateInput` (`<input type="date">`) with label in add-task `.row-right-group` after priority select in `index.html`
- [x] T006 [US1] Add `#editDueDateInput` due date field with label in edit modal after priority group in `index.html`
- [x] T007 [US1] Add read-only due date row `#viewTaskDueDate` in view modal body in `index.html`
- [x] T008 [US1] Wire `addNewTodo()` to read `#todoDueDateInput`, persist `dueDate` (or null), and reset input on success in `index.html`
- [x] T009 [US1] Wire `openEditModal()` and `saveEditedTask()` to populate, save, and clear `dueDate` in `index.html`
- [x] T010 [US1] Wire `openViewModal()` to show `formatDueDateLabel` output or "No due date" in `index.html`

**Checkpoint**: User Story 1 complete—due dates can be set, edited, viewed, and persist

---

## Phase 4: User Story 2 - Spot Overdue Work at a Glance (Priority: P2)

**Goal**: Overdue tasks show red border + red label; due-today tasks show amber border + amber label; Done column never accented

**Independent Test**: Create tasks with yesterday, today, and next-week due dates in To Do; verify colours; move to Done and confirm accents removed (spec US2 scenarios)

**Depends on**: US1 card must display due label (T010 provides view path; T012 adds card label)

### Implementation for User Story 2

- [x] T011 [US2] Add CSS for `.task-due-date`, `.task-card.due-overdue`, and `.task-card.due-today` (border override + label colour) in `index.html`
- [x] T012 [US2] Update `createTaskCardDOM()` to render `Due …` label and apply `due-overdue` / `due-today` classes via `getDueDateUrgency()` when column is not `done` in `index.html`

**Checkpoint**: User Stories 1 and 2 both independently verifiable

---

## Phase 5: User Story 3 - Sort by Due Date (Priority: P3)

**Goal**: Sort dropdown includes soonest/latest; undated tasks sort last; preference persists

**Independent Test**: Create mixed dated/undated tasks; select "Due Date (Soonest)" and "Due Date (Latest)"; refresh and confirm order and preference (spec US3 scenarios)

### Implementation for User Story 3

- [x] T013 [US3] Add `<option value="due-date-asc">Due Date (Soonest)</option>` and `<option value="due-date-desc">Due Date (Latest)</option>` to `#sortBySelect` in `index.html`
- [x] T014 [US3] Add `due-date-asc` / `due-date-desc` branches in `render()` sort logic (undated last; tie-break `createdAt` desc) in `index.html`

**Checkpoint**: All three user stories complete

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Mobile UX, backward compatibility, and manual QA

- [x] T015 Add mobile styles for date inputs (`font-size: 16px`, layout in `.row-right-group`) in `@media (max-width: 768px)` in `index.html`
- [x] T016 Run manual tests T1–T9 from `specs/001-task-due-dates/quickstart.md` in desktop and 375px viewport
- [x] T017 Verify demo/legacy tasks without `dueDate` load and render without errors in `index.html`

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies
- **Foundational (Phase 2)**: Depends on Phase 1 — **blocks all user stories**
- **User Story 1 (Phase 3)**: Depends on Phase 2
- **User Story 2 (Phase 4)**: Depends on Phase 2; integrates with US1 card rendering
- **User Story 3 (Phase 5)**: Depends on Phase 2; benefits from US1 dated tasks for meaningful sort tests
- **Polish (Phase 6)**: Depends on Phases 3–5

### User Story Dependencies

| Story | Depends on | Can test independently after |
|-------|------------|------------------------------|
| US1 (P1) | Foundational | Phase 3 checkpoint |
| US2 (P2) | Foundational + US1 due label on cards | Phase 4 checkpoint |
| US3 (P3) | Foundational | Phase 5 checkpoint (easier with US1 data) |

### Recommended Sequential Order

```text
T001 → T002 → T003 → T004 → T005 → T006 → T007 → T008 → T009 → T010
  → T011 → T012 → T013 → T014 → T015 → T016 → T017
```

All tasks modify `index.html`; execute in order to avoid merge conflicts.

---

## Parallel Example

This feature uses a single source file, so **parallel execution is not recommended**. If splitting work across contributors, assign by phase (one owner per phase) rather than concurrent edits to `index.html`.

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup (T001)
2. Complete Phase 2: Foundational (T002–T004)
3. Complete Phase 3: User Story 1 (T005–T010)
4. **STOP and VALIDATE** using quickstart T1, T2, T5, T6, T8
5. Demo if ready

### Incremental Delivery

1. Foundation (T001–T004)
2. US1 → validate → ship MVP
3. US2 (T011–T012) → validate overdue/today styling
4. US3 (T013–T014) → validate sorting
5. Polish (T015–T017) → full quickstart pass

---

## Notes

- Do not add npm packages, build tools, or split files (constitution Principle I)
- Use existing `saveToStorage()` after every `state.tasks` mutation
- Do not change Kanban move/edit rules (constitution Principle V)
- Commit after each phase checkpoint
