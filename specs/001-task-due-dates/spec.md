# Feature Specification: Task Due Dates

**Feature Branch**: `001-task-due-dates`

**Created**: 2026-06-01

**Status**: Clarified

**Input**: User description: "Hypothesis: If we enable due dates on tasks, users will prioritise work more effectively. Solution Concept: Add an optional due date to each task. Default to no due date. Show overdue tasks in red. Allow sorting by due date. Persist in the same localStorage as existing tasks."

## Clarifications

### Session 2026-06-01

- Q: How should overdue tasks be styled in red on the board? → A: Red left border on the card and red due-date label (Option D).
- Q: How should due dates be formatted on task cards? → A: Short absolute date always (e.g., `Jun 15`; include year when not the current year) (Option A).
- Q: Where should the due date input appear when adding a task? → A: Inline in the main row next to priority, always visible (Option A).
- Q: Should tasks due today have distinct visual styling? → A: Amber/warning accent for due today only (Option B).
- Q: How should the due date appear on task cards? → A: Text prefix before the date (e.g., `Due Jun 15`) (Option A).

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

1. **Given** the add-task form is open, **When** the user views the main input row,
   **Then** an optional due date control is visible inline alongside the priority
   selector without expanding the description area.
2. **Given** the add-task form is open, **When** the user leaves the due date empty and
   submits a valid task, **Then** the task is created with no due date and no due-date
   label appears on the card.
3. **Given** the add-task form is open, **When** the user selects a future calendar
   date and submits a valid task, **Then** the task is created showing that due date on
   the task card as `Due` plus a short absolute date (e.g., `Due Jun 15`) and the
   same format in the task detail view (with year shown when the date is not in the
   current year).
4. **Given** an editable task (not in Done), **When** the user opens the edit dialog,
   sets or changes the due date, and saves, **Then** the updated due date appears on
   the board and the "last edited" metadata reflects the change.
5. **Given** an editable task with a due date, **When** the user clears the due date in
   the edit dialog and saves, **Then** the task no longer displays a due date.
6. **Given** a task in the Done column, **When** the user attempts to edit it,
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
   calendar date), **When** the board renders, **Then** the task card shows a red
   left border and the due-date label is rendered in red (distinct from normal
   priority badge colours).
2. **Given** a task in To Do or In Progress whose due date is exactly today (local
   calendar date), **When** the board renders, **Then** the task card shows an amber
   warning accent (amber left border and amber due-date label) and does not use
   overdue red styling.
3. **Given** a task whose due date is after today, **When** the board renders, **Then**
   the task uses normal card styling (no red or amber due-date accent).
4. **Given** a task with no due date, **When** the board renders, **Then** the task
   uses normal styling regardless of column.
5. **Given** an overdue task, **When** the user moves it to Done, **Then** overdue red
   styling is removed even if the due date remains visible in read-only detail view.
6. **Given** a task due today in To Do or In Progress, **When** the user moves it to
   Done, **Then** amber due-today styling is removed.

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

- What happens when a task becomes overdue at midnight? A task due today should
  switch from amber due-today styling to red overdue styling on the next calendar day
  without user edits.
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
  creating a new task via a date control in the add-task main row (inline with
  priority, always visible); default is no due date.
- **FR-002**: System MUST allow users to set, change, or clear the due date when
  editing a task that is not in the Done column.
- **FR-003**: System MUST display the due date on task cards (when set) and in the
  read-only task detail view, prefixed with `Due` followed by a short absolute date
  (e.g., `Due Jun 15`); include the year when the due date falls outside the current
  calendar year (e.g., `Due Jun 15, 2027`).
- **FR-004**: System MUST treat tasks with no due date exactly as today—no due-date
  label and no overdue styling.
- **FR-005**: System MUST mark a task as overdue when its due date is before the user's
  local calendar date and the task is in To Do or In Progress.
- **FR-006**: System MUST apply overdue styling to overdue task cards using both a
  red left border and a red due-date label, visually separate from priority badge
  colours.
- **FR-007**: System MUST apply due-today styling to tasks in To Do or In Progress
  whose due date equals today's local calendar date, using an amber left border and
  amber due-date label (overdue and due-today are mutually exclusive by date).
- **FR-008**: System MUST NOT apply overdue or due-today accent styling to tasks in
  the Done column.
- **FR-009**: System MUST add "Due Date (Soonest)" and "Due Date (Latest)" options to
  the existing sort control.
- **FR-010**: When sorting by due date, system MUST place tasks without a due date
  after all dated tasks.
- **FR-011**: System MUST persist each task's due date (or absence of one) across browser
  sessions together with existing task data.
- **FR-012**: System MUST preserve backward compatibility so tasks saved before this
  feature continue to load and display without error.
- **FR-013**: System MUST respect existing Kanban rules (Done tasks read-only, workflow
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
  the main board—the due date control is always visible in the add-task main row
  alongside priority.
- **SC-002**: 100% of tasks whose due date is before today and not in Done are
  visually identifiable as overdue (red border and label) within 2 seconds of board
  load; tasks due today show amber accent instead.
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
- Due dates on cards and in the detail view use the prefix `Due` plus a short absolute
  format (e.g., `Due Jun 15`); the year is included when the date is not in the current
  calendar year (e.g., `Due Jun 15, 2027`).
- The browser's local timezone defines "today" for overdue calculations.
- Due date input uses a standard calendar date picker pattern appropriate to the
  platform (native date input or equivalent accessible control).
- Overdue styling uses a red left card border plus a red due-date label; due-today
  styling uses an amber left border plus an amber due-date label (same accent pattern
  as priority borders); priority badge semantics are unchanged.
- No new filter for "overdue only" or due-date range search is in scope for this
  feature.
- Demo/seed tasks ship without due dates; users opt in per task.
- Notification/reminder alerts (email, push, browser notifications) are out of scope.
