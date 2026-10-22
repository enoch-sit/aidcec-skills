---
name: use-tokenproxy
description: Call the TokenProxy API at api.aidcec-ai-agent.com for chat, embeddings, images, video, and account quota. Use when the user mentions TokenProxy, tp-live keys, tokenproxy/fast, tokenproxy/vision, or api.aidcec-ai-agent.com.
---

# Use TokenProxy

Host: `https://api.aidcec-ai-agent.com`. Send `Authorization: Bearer tp-live-…` and `Content-Type: application/json`.

Read the key from `TOKENPROXY_API_KEY`, then `AIDCEC_KEY`, then `~/.aidcec/config.json`. Never ask the user to paste a key into chat, and never print a key.

## Before a long job

`GET /v1/account` returns `allowedModels`, `defaultModel`, `costCapUsd`, `costCapPeriod`, and `spentMicro` (micro-USD). Stop if the remaining cap cannot cover the job. `GET /v1/models` lists the models this key may call. `GET /v1/account/logs` returns this key's chats for evaluation; a group project key keeps about 30 days. See the `evaluate-chatbot` skill.

## Calls

Use the OpenAI paths. `model` must be one of the key's allowed models. Prefer `defaultModel`. Use `tokenproxy/fast` for cheap text and `tokenproxy/vision` when the input includes an image.

```http
POST /v1/chat/completions
{ "model": "tokenproxy/vision", "messages": [{ "role": "user", "content": "…" }], "max_tokens": 1024, "stream": false }
```

```http
POST /v1/embeddings
{ "model": "tokenproxy/embed", "input": "…" }
```

Other routes, same bearer key: `POST /v1/images/generations` (`tokenproxy/image`), `POST /v1/videos` then `GET /v1/videos/:id` (`tokenproxy/video`), `POST /v1/decide` (`tokenproxy/decide`). A missing, unknown, or disabled key is 401. A model the key cannot use is 400.

Docs: https://api.aidcec-ai-agent.com/guides
