# cURL guide

Raw HTTP is the fastest way to prove a key, base URL, and model actually work — with no SDK
in the way.

## Generic request

```bash
curl "$BASE_URL/chat/completions" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "MODEL_ID",
    "messages": [
      { "role": "user", "content": "Say hello in one short sentence." }
    ]
  }'
```

Set the variables per provider, for example:

```bash
export BASE_URL="https://api.groq.com/openai/v1"
export API_KEY="$GROQ_API_KEY"
```

## Authentication

Most providers use a bearer token:

```bash
-H "Authorization: Bearer $API_KEY"
```

Some accept or require a provider-specific header instead. The Gemini native API, for
example, uses:

```bash
-H "x-goog-api-key: $GEMINI_API_KEY"
```

Rules:

- **Never inline the key.** Use `$VARNAME` so it does not land in your shell history or a screenshot.
- Prefer `--header @file` or `--config file` for CI, since command lines are visible in process lists.
- Read the key from a file when scripting:

```bash
curl "$BASE_URL/chat/completions" \
  -H "Authorization: Bearer $(cat ~/.config/llm/key)" \
  -H "Content-Type: application/json" \
  -d @request.json
```

## Request body

```json
{
  "model": "MODEL_ID",
  "messages": [
    { "role": "system", "content": "You are concise." },
    { "role": "user", "content": "Explain HTTP 429 in one sentence." }
  ],
  "temperature": 0.3,
  "max_tokens": 200,
  "stream": false
}
```

| Field | Purpose |
| --- | --- |
| `model` | Provider-specific model ID (required) |
| `messages` | Ordered conversation with `system`, `user`, and `assistant` roles |
| `temperature` | Randomness; lower is more deterministic |
| `max_tokens` | Cap on generated tokens (name varies on some providers) |
| `stream` | `true` for server-sent events |

Keeping the body in a file is easier to diff and reuse:

```bash
cat > request.json <<'JSON'
{ "model": "MODEL_ID", "messages": [{ "role": "user", "content": "Hello" }] }
JSON

curl "$BASE_URL/chat/completions" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d @request.json
```

## Streaming

```bash
curl -N "$BASE_URL/chat/completions" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "MODEL_ID",
    "messages": [{ "role": "user", "content": "Count to five." }],
    "stream": true
  }'
```

`-N` disables buffering so you see `data:` events as they arrive; the stream ends with
`data: [DONE]`.

## Listing models

```bash
curl "$BASE_URL/models" -H "Authorization: Bearer $API_KEY"
```

This is the authoritative answer to "which model IDs are valid right now" — better than any
documentation page, including this one.

## Common HTTP status codes

| Code | Meaning | What to do |
| --- | --- | --- |
| `200` | Success | Parse `choices[0].message.content` |
| `400` | Bad request | Fix the JSON, model ID, or parameter names; do not retry |
| `401` | Unauthenticated | Key missing, wrong, or revoked |
| `403` | Forbidden | Key lacks access to that model, feature, or region |
| `404` | Not found | Wrong path — check whether the base URL already includes `/v1` |
| `413` | Payload too large | Shorten the prompt or context |
| `422` | Unprocessable | Valid JSON, invalid values |
| `429` | Rate limited or out of quota | Back off exponentially; honour `Retry-After` |
| `500`/`502`/`503`/`504` | Upstream problem | Retry with backoff; consider a fallback provider |

## Debugging

```bash
# response headers plus body
curl -i "$BASE_URL/chat/completions" -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" -d @request.json

# status code only
curl -s -o /dev/null -w '%{http_code}\n' "$BASE_URL/models" \
  -H "Authorization: Bearer $API_KEY"

# full transcript, including TLS and redirects (redact before sharing)
curl -v "$BASE_URL/models" -H "Authorization: Bearer $API_KEY"

# timing breakdown
curl -s -o /dev/null -w 'dns:%{time_namelookup} connect:%{time_connect} ttfb:%{time_starttransfer} total:%{time_total}\n' \
  "$BASE_URL/models" -H "Authorization: Bearer $API_KEY"

# pretty-print with jq
curl -s "$BASE_URL/chat/completions" -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" -d @request.json | jq '.choices[0].message.content'
```

Checklist when a call fails:

1. Does `GET $BASE_URL/models` work? If not, the problem is the key or base URL, not the request body.
2. Is the path doubled (`/v1/v1/...`) or missing `/v1`?
3. Is the model ID exactly as the provider spells it, including case?
4. Is `Content-Type: application/json` present?
5. Is your JSON valid? Pipe it through `jq .` first.
6. Look at rate-limit headers in the response before assuming your quota is gone.

## Security

- Never paste a real key into a shared terminal, issue, gist, or CI log.
- Redact `Authorization` headers from any `-v` output you share.
- Prefer `-d @file` over long inline bodies containing customer data.
- Rotate a key the moment it appears in history, a log, or a screenshot.
- Use `set +o history` or a leading space (with `HISTCONTROL=ignorespace`) for one-off commands with sensitive values.

## See also

- [How OpenAI-compatible APIs work](openai-compatible-apis.md)
- [Python guide](python.md)
