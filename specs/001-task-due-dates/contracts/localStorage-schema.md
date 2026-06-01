# Contract: localStorage Task Schema Extension

**Key**: `todolist`  
**Format**: JSON string of `state` object

## Task object — additive field

```json
{
  "id": "task-1717234567890-123",
  "title": "Example task",
  "desc": "",
  "priority": "medium",
  "column": "todo",
  "createdAt": 1717234567890,
  "editedAt": null,
  "completed": false,
  "dueDate": "2026-06-15"
}
```

| Field | Type | Constraint |
|-------|------|------------|
| `dueDate` | `string \| null` | Optional. When string, MUST be `YYYY-MM-DD`. When absent, treat as `null`. |

## Sort field extension

`state.sortBy` MAY include:

| Value | Behavior |
|-------|----------|
| `due-date-asc` | Soonest due date first; undated last; tie → `createdAt` desc |
| `due-date-desc` | Latest due date first; undated last; tie → `createdAt` desc |

Existing values unchanged.

## Compatibility guarantee

- Readers MUST NOT require `dueDate` on every task.
- Writers MUST NOT change `LOCAL_STORAGE_KEY` or top-level `state` shape.
- Writers MUST call existing `saveToStorage()` after mutating tasks.

## Example minimal diff (undated task)

```json
{
  "id": "demo-1",
  "title": "Set up production CI/CD pipelines",
  "desc": "...",
  "priority": "high",
  "column": "todo",
  "createdAt": 1717230000000,
  "editedAt": null,
  "completed": false
}
```

Valid without `dueDate`.
