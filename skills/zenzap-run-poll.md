---
name: Run a poll and collect votes
description: Create a poll in a Zenzap topic and cast or retract votes on behalf of the bot.
api: openapi/zenzap-openapi-original.yml
operations: [createPoll, createPollVote, deletePollVote]
---

# Run a poll and collect votes

Create a poll in a topic and manage votes programmatically.

## Auth
- Base URL: `https://api.zenzap.co` (EU) or `https://api.us.zenzap.co` (US).
- OAuth scope: `poll:write`. The bot must be a member of the topic.

## Steps
1. **createPoll** — `POST /v2/polls` with `topicId`, a question, and options. Returns the poll ID and option IDs.
2. **createPollVote** — `POST /v2/polls/{pollId}/votes` with the chosen `optionId`. Each `{pollId, optionId, voter}` combination is idempotent — resubmitting the same vote is a no-op. Capture the returned vote ID.
3. **deletePollVote** — `DELETE /v2/polls/{pollId}/votes/{voteId}` to retract a vote.

## Rules
- `poll_vote.created` / `poll_vote.deleted` webhook events fire for votes in topics the bot is in.
- Rate limit 1,000/60s per key; back off on `429`.
