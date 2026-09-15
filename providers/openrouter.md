# OpenRouter

A single OpenAI-compatible gateway that routes requests to many upstream model providers, including a rotating set of free-to-use model variants.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | OpenRouter |
| Website | <https://openrouter.ai/> |
| API documentation | <https://openrouter.ai/docs> |
| Base URL (OpenAI-compatible) | `https://openrouter.ai/api/v1` |
| Environment variable | `OPENROUTER_API_KEY` |
| OpenAI-compatible | Yes |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://openrouter.ai/>
- API documentation: <https://openrouter.ai/docs>
- API keys / console: <https://openrouter.ai/keys>

## Free-tier information

OpenRouter lists model variants with a `:free` suffix that can be called without per-token cost, subject to its own rate and daily limits. Which models are free changes frequently.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Create an API key in the OpenRouter dashboard and send it as `Authorization: Bearer YOUR_API_KEY`. Optional `HTTP-Referer` and `X-Title` headers attribute traffic to your app.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export OPENROUTER_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://openrouter.ai/api/v1
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `openai/gpt-oss-120b`
- `meta-llama/llama-3.3-70b-instruct`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["OPENROUTER_API_KEY"],   # never hard-code the key
    base_url="https://openrouter.ai/api/v1",
)

response = client.chat.completions.create(
    model="openai/gpt-oss-120b",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.OPENROUTER_API_KEY, // never hard-code the key
  baseURL: "https://openrouter.ai/api/v1",
});

const response = await client.chat.completions.create({
  model: "openai/gpt-oss-120b",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://openrouter.ai/api/v1/chat/completions" \
  -H "Authorization: Bearer $OPENROUTER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "openai/gpt-oss-120b",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Yes.** Designed as a drop-in OpenAI replacement, including streaming and tool calling where the upstream model supports them. Model IDs are namespaced as `vendor/model`.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Comparing many models through one key and one client
- Fallback routing when a single upstream provider is unavailable
- Experimenting with free model variants before committing to a provider

## Limitations

- Free variants are typically rate-limited more aggressively and may be withdrawn
- Behaviour, context length, and feature support depend on the upstream provider

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [Quickstart](https://openrouter.ai/docs/quickstart)
- [Models list](https://openrouter.ai/models)
- [Limits](https://openrouter.ai/docs/api-reference/limits)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
