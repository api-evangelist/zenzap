---
name: Create a topic and post a message
description: Create a Zenzap topic with members and post a message to it as the bot, optionally reacting.
api: openapi/zenzap-openapi-original.yml
operations: [createTopic, createMessage, addMessageReaction]
---

# Create a topic and post a message

Use the Zenzap External Integration API to spin up a conversation and post into it as your bot.

## Auth
- Base URL depends on data residency: `https://api.zenzap.co` (EU) or `https://api.us.zenzap.co` (US).
- Either a static API key (with `X-Signature` HMAC-SHA256 + `X-Timestamp`) or an OAuth `client_credentials` bearer token. OAuth scopes needed: `channel:write`, `message:send`.

## Steps
1. **createTopic** — `POST /v2/topics` with the member IDs and a name/description. Your bot is added automatically. Capture the returned `topicId`.
2. **createMessage** — `POST /v2/messages` with `topicId` and `text`. The bot appears as sender. To mention someone, embed `<@profileId>` in the text.
3. **addMessageReaction** (optional) — `POST /v2/messages/{messageId}/reactions` to react. This is idempotent per `{messageId, bot, reaction}` (201 first time, 200 on repeat).

## Rules
- Rate limit: 1,000 requests per API key per 60s; back off on `429`.
- Errors return plain-text bodies; `403` usually means the bot is not a member of the topic or lacks the scope.
