# Contract: Due Date UI Surfaces

**Target file**: `index.html`  
**Feature**: 001-task-due-dates

## Input surfaces

| Element ID | Context | Control | Default | Cleared on |
|------------|---------|---------|---------|------------|
| `todoDueDateInput` | Add-task main row | `<input type="date">` | empty | successful add |
| `editDueDateInput` | Edit modal | `<input type="date">` | task value | modal close |

**Layout**: `todoDueDateInput` inline in `.row-right-group` after `#todoPriorityInput`, always visible (not in expanded desc row).

## Output surfaces

| Element ID | Context | Content when undated | Content when dated |
|------------|---------|----------------------|-------------------|
| Task card `.task-due-date` | Board card header area | hidden | `Due Jun 15` (formatted) |
| `#viewTaskDueDate` | View modal | `No due date` | `Due Jun 15, 2027` if needed |

## Card CSS classes

Applied in `createTaskCardDOM()` when `column !== 'done'`:

| Class | Condition | Visual |
|-------|-----------|--------|
| `due-overdue` | `dueDate < local today` | Red left border override; red `.task-due-date` text |
| `due-today` | `dueDate === local today` | Amber left border override; amber `.task-due-date` text |
| (none) | future date or undated | Priority border only; muted due label |

Never apply `due-overdue` or `due-today` when `column === 'done'`.

## Sort dropdown

Add options to `#sortBySelect`:

```html
<option value="due-date-asc">Due Date (Soonest)</option>
<option value="due-date-desc">Due Date (Latest)</option>
```

## User feedback (unchanged patterns)

| Event | Feedback |
|-------|----------|
| Task created with due date | success toast (existing) |
| Task updated | success toast (existing) |
| Invalid date (browser prevents) | native input constraint |

No new toast types required.

## Accessibility

- `<label for="todoDueDateInput">` and `<label for="editDueDateInput">` associated with inputs
- Due label on card is plain text (not sole indicator of urgency — border also changes)
