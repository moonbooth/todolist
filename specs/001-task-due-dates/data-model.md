# Data Model: Task Due Dates

**Feature**: 001-task-due-dates  
**Storage**: `localStorage` key `todolist` → JSON `state` object (unchanged top-level shape)

## State object (extended)

```text
state = {
  tasks: Task[],
  filterPriority: 'all' | 'low' | 'medium' | 'high',
  sortBy: 'date-desc' | 'date-asc' | 'priority-desc' | 'title-asc'
          | 'due-date-asc' | 'due-date-desc',   // NEW
  searchQuery: string,
  activeTab: 'todo' | 'progress' | 'done'
}
```

## Task entity (extended)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | yes | Unchanged |
| `title` | string | yes | 3–40 chars |
| `desc` | string | yes | ≤150 chars; may be `''` |
| `priority` | `'low' \| 'medium' \| 'high'` | yes | Unchanged |
| `column` | `'todo' \| 'progress' \| 'done'` | yes | Unchanged |
| `createdAt` | number | yes | Unix ms timestamp |
| `editedAt` | number \| null | yes | Unix ms or null |
| `completed` | boolean | yes | Unchanged |
| `dueDate` | string \| null | no | ISO date `YYYY-MM-DD`; absent or null = undated |

### Validation rules

| Rule | On create | On edit |
|------|-----------|---------|
| `dueDate` omitted or empty input | Store `null` | Store `null` |
| `dueDate` present | Must match `/^\d{4}-\d{2}-\d{2}$/` and be valid calendar date | Same |
| Past due date allowed | yes | yes (shows overdue styling if active column) |
| Done column edit | N/A — due date not editable | Blocked by existing edit guard |

### Derived attributes (not persisted)

| Derived | Logic |
|---------|--------|
| `urgency` | `'none'` if no dueDate or column=`done`; `'overdue'` if dueDate < local today; `'today'` if equal; `'future'` if dueDate > today |
| `dueDateLabel` | `null` if undated; else `` `Due ${formatted}` `` per research.md |

## Relationships

- **Sort preference → tasks**: `state.sortBy` controls in-memory ordering at render; no change to task entity.
- **Kanban column → urgency display**: `column === 'done'` suppresses overdue/today accent classes only; `dueDate` value retained for view modal.

## State transitions (due-date relevant)

```text
[undated] --user sets date--> [dated]
[dated] --user clears date--> [undated]
[dated, due today, todo/progress] --midnight--> [dated, overdue] (on next render/load)
[dated, overdue] --move to done--> [dated, no accent styling]
[dated] --move back to progress--> urgency recalculated from dueDate vs today
```

Kanban move/complete rules unchanged (Principle V).

## Backward compatibility

Legacy tasks without `dueDate` property:

- Treated as `dueDate: null`
- No due label on card
- Sort by due date: grouped with undated (last)
- No load errors
