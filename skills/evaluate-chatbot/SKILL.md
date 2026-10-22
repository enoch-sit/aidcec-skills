---
name: evaluate-chatbot
description: >-
  Evaluate a student chatbot from TokenProxy request logs. Use when the user
  mentions evaluating a chatbot, token usage, tool usage, A/B prompts, a golden
  set, cases.jsonl, or GET /v1/account/logs.
---

# Evaluate a chatbot from its logs

The gateway already stores each chat. Fetch that one list and judge it yourself. Do not add an evaluation service, and do not ask for a session id.

Host: `https://api.aidcec-ai-agent.com`. Read the key from `TOKENPROXY_API_KEY`, then `AIDCEC_KEY`, then `~/.aidcec/config.json`. Never ask the user to paste a key into chat, and never print a key.

A group project key (`aiep:group:…` on inclass → My model key) keeps about 30 days of chats, up to 50 MB. Any other key returns recent errors only, with `source: "recent"`.

Logs are redacted. Do not try to re-identify classmates.

## 1. Fetch

Save every page to `logs.json`. Follow `next` until it is null. `days` is 1 to 30 (default 7). `limit` is at most 1000.

```sh
curl -sS "https://api.aidcec-ai-agent.com/v1/account/logs?days=7" \
  -H "Authorization: Bearer $TOKENPROXY_API_KEY" \
  -o logs.json
```

The next page is the same URL plus `&before=` set to the previous `next` value.

A group key returns `{ source: "archive", retentionDays, records, next, errors }`. Each record is `{ at, requestId, model, inputTokens, outputTokens, costMicro, body }`. `body` is `{ messages, output }` with secrets masked. `errors` lists recent failed calls (`requestId`, `at`, `model`, `status`, `errorCode`) and is only the gateway's recent memory, not the 30 day archive.

## 2. Rebuild conversations

Each request sends the whole `messages` list. A later record continues an earlier one when its `messages` start with the earlier record's `messages` followed by that record's `output`. Group those into one conversation. A page may also send `x-site-session` or a `sessionId`; use it when it is present. It is optional, and the gateway does not require it.

## 3. Token and cost

Sum `inputTokens`, `outputTokens`, and `costMicro` (micro-USD, so divide by 1,000,000 for dollars). Break the totals down by `model`. Point at the longest prompts.

## 4. Tool usage

Count messages with `role: "tool"`, and count tool names in the history. Only tool results the page sent back are visible. A tool call the model made, which the page never returned, is absent.

## 5. Failures

List `errors`. Also list records whose `output` is empty or a refusal, and user questions that repeat because the first answer failed.

## 6. A/B

Run the same prompts against prompt version A and version B. Tag the version in the system prompt, or split the records by time. Report the pass rate and the cost of each version. Say which version you would keep, and why.

## 7. Golden set

Turn 10 to 20 real failures into `cases.jsonl`, one JSON object per line: the user input, and the behaviour you expected. After each change to the chatbot, run those cases again and compare.

## Prompt to give the agent

Paste this to the coding agent after the skill is installed:

```text
Read the evaluate-chatbot skill. Fetch GET /v1/account/logs?days=7 with my group key from the environment, follow next, and save logs.json. Then report token and cost totals by model, which tools ran, the failed or repeated chats, and a cases.jsonl of 10 to 20 real failures. Do not print the key.
```
