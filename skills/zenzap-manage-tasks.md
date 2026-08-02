---
name: Create and track tasks in a topic
description: Create tasks in a Zenzap topic, list and read them, and update status or fields.
api: openapi/zenzap-openapi-original.yml
operations: [createTask, listTasks, getTask, patchTask]
---

# Create and track tasks in a topic

Manage work items attached to a Zenzap topic as the bot.

## Auth
- Base URL: `https://api.zenzap.co` (EU) or `https://api.us.zenzap.co` (US).
- OAuth scopes: `task:write` for create/update, `task:read` for list/get.

## Steps
1. **createTask** — `POST /v2/tasks` with `topicId`, `title`, optional `text`, `assignee`, and `dueDate` (Unix ms). Returns the task ID.
2. **listTasks** — `GET /v2/tasks` filtered by `topicId`, `status`, or `assignee`; cursor pagination via `limit` + `cursor`.
3. **getTask** — `GET /v2/tasks/{taskId}` for a single task.
4. **patchTask** — `PATCH /v2/tasks/{taskId}` for partial updates (e.g. change status to done, reassign, move the due date).

## Rules
- Task changes broadcast `task` snapshot messages / webhook events with actions like `Added`, `Updated`, `MarkedAsDone`.
- Rate limit 1,000/60s per key; retry `429` with exponential backoff.
