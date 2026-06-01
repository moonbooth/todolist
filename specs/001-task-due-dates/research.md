# Research: Task Due Dates

**Feature**: 001-task-due-dates  
**Date**: 2026-06-01

## 1. Due date storage format

**Decision**: Store `dueDate` as an ISO 8601 calendar string `YYYY-MM-DD` or omit/`null` when undated.

**Rationale**: Matches native `<input type="date">` value format; lexicographic string comparison equals chronological order; no timezone drift for date-only semantics when comparing calendar days via parsed local midnight.

**Alternatives considered**:
- Unix timestamp at midnight — harder to debug in localStorage; timezone edge cases on read
- `MM/DD/YYYY` string — non-sortable lexicographically
- Separate `{ day, month, year }` object — verbose without benefit

## 2. Date input control

**Decision**: Native `<input type="date">` for add form and edit modal.

**Rationale**: Zero dependencies; accessible; mobile browsers provide platform date picker; constitution forbids new libraries.

**Alternatives considered**:
- Custom calendar widget — excessive scope vs Principle IV
- Text input with manual parsing — poor UX and validation burden

## 3. "Today" and overdue calculation

**Decision**: Compare `dueDate` string to `getLocalDateString(new Date())` using local timezone; overdue if `dueDate < today`; due-today if `dueDate === today`.

**Rationale**: Aligns with spec clarifications; simple string compare after normalizing both to `YYYY-MM-DD`.

**Alternatives considered**:
- UTC midnight — would misclassify near timezone boundaries for local users
- End-of-day timestamp — spec explicitly date-only

## 4. Due date display format

**Decision**: `Due ${shortMonth} ${day}` via `toLocaleDateString('en-US', { month: 'short', day: 'numeric' })`; append `, ${year}` when year ≠ current year.

**Rationale**: Matches clarification session Option A + text prefix Option A; consistent with existing `formatRelativeTime` / `formatFullTime` locale usage.

**Alternatives considered**:
- Relative labels ("Due tomorrow") — rejected in clarification
- Icon prefix — rejected in clarification

## 5. Visual urgency on cards

**Decision**: Modifier classes `due-overdue` and `due-today` on `.task-card` override left border colour; `.task-due-date` span inherits red or amber text. Not applied in Done column.

**Rationale**: Reuses existing priority border-width pattern; clarification specifies border + label for both states.

**Alternatives considered**:
- Full-card red background — rejected as too heavy
- Replace priority border entirely — would lose priority signal; override is sufficient

## 6. Sort tie-breaking

**Decision**: When `dueDate` equal or both undated, secondary sort by `createdAt` descending (newest first).

**Rationale**: Deterministic order; deferred in clarify as low impact; matches existing default sort bias.

**Alternatives considered**:
- Title alphabetical tie-break — less intuitive for task boards
- Preserve manual drag order — not tracked in current app

## 7. Backward compatibility

**Decision**: No explicit migration; `task.dueDate` optional; `loadFromStorage` unchanged except tasks naturally lack field.

**Rationale**: FR-012; JSON parse preserves unknown shape; demo tasks need no update.

**Alternatives considered**:
- One-time migration setting `dueDate: null` on all tasks — unnecessary write on every first load
