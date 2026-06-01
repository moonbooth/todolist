# Feature Specification: Task Due Dates

**Feature Branch**: `001-task-due-dates`

**Created**: 2026-06-01

**Status**: Draft

**Input**: User description: "Hypothesis: If we enable due dates on tasks, users will prioritise work more effectively. Solution Concept: Add an optional due date to each task. Default to no due date. Show overdue tasks in red. Allow sorting by due date. Persist in the same localStorage as existing tasks."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Set an Optional Due Date (Priority: P1)

A user creating or editing a task wants to optionally assign a calendar due date so they
can plan when work should be finished, without forcing every task to have a deadline.

**Why this priority**: Due dates are the foundation of the feature; without the ability
to set them, overdue highlighting and sorting deliver no value.

**Independent Test**: Create a task with a due date selected, save it, refresh the
browser, and confirm the due date is still shown on the task. Create another task
leaving due date empty and confirm it behaves like today's tasks (no due date shown).

**Acceptance Scenarios**:

1. **Given** the add-task form is open, **When** the user leaves the due date empty and
   submits a valid task, **Then** the task is created with no due date and no due-date
   label appears on the card.
2. **Given** the add-task form is open, **When** the user selects a future calendar
   date and submits a valid task, **Then** the task is created showing that due date on
   the task card and in the task detail view.
3. **Given** an editable task (not in Done), **When** the user opens the edit dialog,
   sets or changes the due date, and saves, **Then** the updated due date appears on
   the board and the "last edited" metadata reflects the change.
4. **Given** an editable task with a due date, **When** the user clears the due date in
   the edit dialog and saves, **Then** the task no longer displays a due date.
5. **Given** a task in the Done column, **When** the user attempts to edit it,
   **Then** existing read-only rules still apply (due date cannot be changed).

---

### User Story 2 - Spot Overdue Work at a Glance (Priority: P2)

A user scanning their board wants overdue tasks to stand out visually so they can
reprioritise before deadlines pass unnoticed.

**Why this priority**: The hypothesis depends on users *acting* on due dates; visual
urgency is the primary nudge mechanism requested.

**Independent Test**: Create a task with yesterday's due date in To Do or In Progress.
Confirm the task card uses distinct red overdue styling. Move the same task to Done and
confirm overdue styling no longer applies.

**Acceptance Scenarios**:

1. **Given** a task in To Do or In Progress whose due date is before today (local
   calendar date), **When** the board renders, **Then** the task card is styled with
   clear red overdue indication (distinct from normal priority colours).
2. **Given** a task whose due date is today or in the future, **When** the board
   renders, **Then** the task does not use overdue red styling.
3. **Given** a task with no due date, **When** the board renders, **Then** the task
   uses normal styling regardless of column.
4. **Given** an overdue task, **When** the user moves it to Done, **Then** overdue red
   styling is removed even if the due date remains visible in read-only detail view.

---

### User Story 3 - Sort by Due Date (Priority: P3)

A user with many tasks wants to reorder the board by due date so the most urgent
deadlines surface first.

**Why this priority**: Sorting completes the prioritisation loop started by due dates
and overdue highlighting; it is additive once dates exist on tasks.

**Independent Test**: Create three tasks with different due dates (or no due date),
select "Due Date (Soonest)" in the sort control, and confirm order matches soonest
first with undated tasks last.

**Acceptance Scenarios**:

1. **Given** multiple tasks with due dates in the same column, **When** the user selects
   "Due Date (Soonest)" from the sort dropdown, **Then** tasks are ordered with the
   nearest due date first within each column.
2. **Given** tasks with and without due dates, **When** the user sorts by due date
   (soonest), **Then** tasks without a due date appear after all dated tasks.
3. **Given** the user selects "Due Date (Latest)" from the sort dropdown, **When** the
   board re-renders, **Then** tasks are ordered with the furthest due date first and
   undated tasks appear last.
4. **Given** a sort-by-due-date option is active, **When** the user refreshes the
   browser, **Then** the same sort preference is restored along with task data.

---

### Edge Cases

- What happens when a task becomes overdue at midnight? The board should reflect overdue
  status on the next page load or render without requiring the user to edit the task.
- How are existing tasks without a due date field handled? They MUST behave as undated
  tasks with no migration friction for the user.
- What if the user sets a due date in the past when creating a task? The task is saved
  and immediately shown as overdue (if not in Done).
- Can due date be set on demo/seed tasks? Existing demo tasks remain undated unless the
  user edits them.
- Does search/filter interact with due dates? Search continues to match title and
  description only; due date is not a search field in this feature.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST allow users to optionally set a calendar due date when
  creating a new task; default is no due date.
- **FR-002**: System MUST allow users to set, change, or clear the due date when
  editing a task that is not in the Done column.
- **FR-003**: System MUST display the due date on task cards (when set) and in the
  read-only task detail view.
- **FR-004**: System MUST treat tasks with no due date exactly as today—no due-date
  label and no overdue styling.
- **FR-005**: System MUST mark a task as overdue when its due date is before the user's
  local calendar date and the task is in To Do or In Progress.
- **FR-006**: System MUST apply distinct red overdue styling to overdue task cards
  that is visually separate from priority badge colours.
- **FR-007**: System MUST NOT apply overdue styling to tasks in the Done column.
- **FR-008**: System MUST add "Due Date (Soonest)" and "Due Date (Latest)" options to
  the existing sort control.
- **FR-009**: When sorting by due date, system MUST place tasks without a due date
  after all dated tasks.
- **FR-010**: System MUST persist each task's due date (or absence of one) across browser
  sessions together with existing task data.
- **FR-011**: System MUST preserve backward compatibility so tasks saved before this
  feature continue to load and display without error.
- **FR-012**: System MUST respect existing Kanban rules (Done tasks read-only, workflow
  transitions unchanged).

### Key Entities

- **Task** (extended): Existing task attributes (title, description, priority, column,
  timestamps, completion) plus optional `dueDate` stored as a calendar date (date-only,
  no time component). Absence of `dueDate` means undated.
- **Sort preference** (extended): Existing sort options plus `due-date-asc` (soonest
  first) and `due-date-desc` (latest first), persisted with other board preferences.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Users can add a due date to a new task in a single flow without leaving
  the main board (same interaction pattern as setting priority).
- **SC-002**: 100% of tasks whose due date is before today and not in Done are
  visually identifiable as overdue within 2 seconds of board load (no user action
  required beyond opening the app).
- **SC-003**: When "Due Date (Soonest)" is selected, tasks in a column appear in
  correct chronological order in a manual test of at least 5 tasks with mixed dates and
  undated entries.
- **SC-004**: After closing and reopening the browser, due dates and sort preference
  match the state before closure for all tasks in the test set.
- **SC-005**: Existing tasks without due dates load without data loss or error messages
  after the feature is deployed.

## Assumptions

- Due dates are **date-only** (no time-of-day); "today" is not overdue—only dates
  strictly before the user's local calendar day count as overdue.
- The browser's local timezone defines "today" for overdue calculations.
- Due date input uses a standard calendar date picker pattern appropriate to the
  platform (native date input or equivalent accessible control).
- Red overdue styling extends the existing design system (e.g., red border, text, or
  background accent) without replacing priority badge semantics.
- No new filter for "overdue only" or due-date range search is in scope for this
  feature.
- Demo/seed tasks ship without due dates; users opt in per task.
- Notification/reminder alerts (email, push, browser notifications) are out of scope.
