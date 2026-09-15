# Cohere

Enterprise-oriented models for retrieval-augmented generation, embeddings, and reranking, plus a Command family of chat models.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | Cohere |
| Website | <https://cohere.com/> |
| API documentation | <https://docs.cohere.com/> |
| Base URL (OpenAI-compatible) | `https://api.cohere.ai/compatibility/v1` |
| Base URL (native API) | `https://api.cohere.com/v2` |
| Environment variable | `COHERE_API_KEY` |
| OpenAI-compatible | Via compatibility API |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://cohere.com/>
- API documentation: <https://docs.cohere.com/>
- API keys / console: <https://dashboard.cohere.com/api-keys>

## Free-tier information

Cohere issues rate-limited trial API keys that are documented as being for evaluation and prototyping rather than production use.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Create a trial or production key in the Cohere dashboard and send it as `Authorization: Bearer YOUR_API_KEY`.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export COHERE_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://api.cohere.ai/compatibility/v1
```

Native (non-OpenAI-shaped) API:

```text
https://api.cohere.com/v2
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `command-r-plus`
- `command-r`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["COHERE_API_KEY"],   # never hard-code the key
    base_url="https://api.cohere.ai/compatibility/v1",
)

response = client.chat.completions.create(
    model="command-r-plus",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.COHERE_API_KEY, // never hard-code the key
  baseURL: "https://api.cohere.ai/compatibility/v1",
});

const response = await client.chat.completions.create({
  model: "command-r-plus",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://api.cohere.ai/compatibility/v1/chat/completions" \
  -H "Authorization: Bearer $COHERE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "command-r-plus",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Via compatibility API.** Cohere publishes a Compatibility API that lets the OpenAI SDKs call Cohere models. The native v2 API is the fuller interface, especially for rerank, embed, and RAG-specific parameters.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Retrieval-augmented generation pipelines with reranking
- High-quality multilingual embeddings
- Enterprise search over private corpora

## Limitations

- Trial keys are explicitly not for production workloads
- Rerank and some RAG features are only in the native API, not the compatibility layer

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [Compatibility API](https://docs.cohere.com/docs/compatibility-api)
- [Chat API reference](https://docs.cohere.com/reference/chat)
- [Rate limits](https://docs.cohere.com/docs/rate-limits)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
