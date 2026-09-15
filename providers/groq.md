# Groq

Groq serves open-weight models on its own LPU inference hardware and is known for very high token throughput on chat completions.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | Groq |
| Website | <https://groq.com/> |
| API documentation | <https://console.groq.com/docs> |
| Base URL (OpenAI-compatible) | `https://api.groq.com/openai/v1` |
| Environment variable | `GROQ_API_KEY` |
| OpenAI-compatible | Yes |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://groq.com/>
- API documentation: <https://console.groq.com/docs>
- API keys / console: <https://console.groq.com/keys>

## Free-tier information

Groq offers a free developer tier on GroqCloud with rate limits per model, alongside paid on-demand tiers.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Create an API key in the GroqCloud console and send it as `Authorization: Bearer YOUR_API_KEY`.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export GROQ_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://api.groq.com/openai/v1
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `llama-3.3-70b-versatile`
- `openai/gpt-oss-120b`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["GROQ_API_KEY"],   # never hard-code the key
    base_url="https://api.groq.com/openai/v1",
)

response = client.chat.completions.create(
    model="llama-3.3-70b-versatile",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.GROQ_API_KEY, // never hard-code the key
  baseURL: "https://api.groq.com/openai/v1",
});

const response = await client.chat.completions.create({
  model: "llama-3.3-70b-versatile",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://api.groq.com/openai/v1/chat/completions" \
  -H "Authorization: Bearer $GROQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama-3.3-70b-versatile",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Yes.** Groq exposes an OpenAI-compatible `/chat/completions` endpoint, so the official `openai` SDKs work by changing the base URL, key, and model.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Latency-sensitive chat and agent loops
- High-volume batch summarisation or classification
- Streaming UIs where perceived speed matters

## Limitations

- Catalogue is focused on open-weight models rather than frontier closed models
- Models are added and retired regularly; pin nothing without checking the model list endpoint

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [Quickstart](https://console.groq.com/docs/quickstart)
- [OpenAI compatibility](https://console.groq.com/docs/openai)
- [Rate limits](https://console.groq.com/docs/rate-limits)
- [Supported models](https://console.groq.com/docs/models)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
