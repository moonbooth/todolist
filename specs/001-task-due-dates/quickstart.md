# Quickstart: Task Due Dates

**Feature**: 001-task-due-dates  
**Branch**: `001-task-due-dates`

## Prerequisites

- Modern browser
- Open `index.html` from repo root (double-click or local static server)
- Optional: DevTools → Application → Local Storage → verify key `todolist`

## Setup

1. Checkout branch `001-task-due-dates` (after implementation).
2. Open `index.html`.
3. If old data interferes, clear `localStorage` key `todolist` or use incognito window.

## Manual test script

### T1 — Add undated task (P1)

1. Enter title `Test undated` (≥3 chars).
2. Leave due date empty.
3. Click Add.
4. **Expect**: Card has no `Due …` label; no red/amber border override.

### T2 — Add future-dated task (P1)

1. Add task `Test future` with due date one week ahead.
2. **Expect**: Card shows `Due Mmm DD`; normal priority border; no red/amber.
3. Refresh page.
4. **Expect**: Same due date persists.

### T3 — Overdue styling (P2)

1. Add task `Test overdue` with yesterday's date.
2. Leave in To Do or In Progress.
3. **Expect**: Red left border + red `Due …` label.
4. Move to Done.
5. **Expect**: Red/amber styling gone; due date still visible in view modal.

### T4 — Due today styling (P2)

1. Add task `Test today` with today's date.
2. **Expect**: Amber border + amber due label (not red).

### T5 — Edit due date (P1)

1. Edit an active task; change due date; save.
2. **Expect**: Card updates; view modal shows new date; Last Edited updates.
3. Clear due date in edit modal; save.
4. **Expect**: Due label removed from card.

### T6 — Done read-only (P1)

1. Complete a task to Done.
2. **Expect**: No edit button; due date unchanged in view modal.

### T7 — Sort soonest (P3)

1. Create ≥3 tasks with different due dates plus one undated.
2. Select **Due Date (Soonest)**.
3. **Expect**: Nearest date first within each column; undated last.
4. Refresh.
5. **Expect**: Sort preference retained.

### T8 — Backward compatibility (P1)

1. Load app with pre-feature localStorage (tasks without `dueDate` field) OR use seeded demo tasks.
2. **Expect**: App loads with no errors; demo tasks show no due date.

### T9 — Mobile smoke (G2)

1. DevTools → responsive 375px width.
2. **Expect**: Due date input visible in add row; date picker usable; no body double-scroll.

## Pass criteria

All expectations match spec success criteria SC-001 through SC-005.

## Troubleshooting

| Issue | Check |
|-------|-------|
| Due date not saving | Application tab → `todolist` JSON includes `dueDate` |
| Wrong overdue colour | System date / timezone; compare `dueDate` string to today |
| Sort unchanged | `state.sortBy` value in localStorage |
