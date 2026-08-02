---
name: Build an event-driven bot with long polling
description: Receive Zenzap events via long polling, react to messages, and mark them read.
api: openapi/zenzap-openapi-original.yml
operations: [getUpdates, createMessage, markMessageRead]
---

# Build an event-driven bot with long polling

For bots configured with polling delivery mode, pull events and respond.

## Auth
- Base URL: `https://api.zenzap.co` (EU) or `https://api.us.zenzap.co` (US).
- OAuth scopes: `updates:read`, plus `message:send` / `message:write` to respond.

## Steps
1. **getUpdates** — `GET /v2/updates` with `offset`, `limit`, and `timeout` (long poll). Each event carries `id`, `type` (e.g. `message.created`), `eventVersion`, `timestamp`, `data`. Deduplicate on `id`.
2. Handle the event `type` (message.created, reaction.added, member.added, poll_vote.created, ...). Advance `offset` past processed events.
3. **createMessage** — `POST /v2/messages` to reply in the same `topicId`.
4. **markMessageRead** — `POST /v2/messages/{messageId}/read` to acknowledge. Idempotent — safe to repeat.

## Alternative
- Instead of polling, configure a webhook endpoint; deliveries are HMAC-SHA256 signed (`X-Zenzap-Signature`, `X-Zenzap-Timestamp`) and carry `X-Zenzap-Delivery-Id`. Verify the signature over `{timestamp}.{payload}` before trusting the event.

## Rules
- Attachment URLs expire after 60 minutes — download promptly.
- Webhooks retry up to 3x and auto-pause after 10 consecutive failures.
