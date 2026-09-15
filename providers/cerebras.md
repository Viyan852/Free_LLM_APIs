# Cerebras Inference

Inference on Cerebras wafer-scale hardware, focused on very low latency for open-weight chat models.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | Cerebras Inference |
| Website | <https://cerebras.ai/inference> |
| API documentation | <https://inference-docs.cerebras.ai/> |
| Base URL (OpenAI-compatible) | `https://api.cerebras.ai/v1` |
| Environment variable | `CEREBRAS_API_KEY` |
| OpenAI-compatible | Yes |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://cerebras.ai/inference>
- API documentation: <https://inference-docs.cerebras.ai/>
- API keys / console: <https://cloud.cerebras.ai/>

## Free-tier information

Cerebras documents a free tier in Cerebras Cloud with per-model request and token limits, plus paid tiers for higher throughput.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Create an API key in Cerebras Cloud and send it as `Authorization: Bearer YOUR_API_KEY`.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export CEREBRAS_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://api.cerebras.ai/v1
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `llama-3.3-70b`
- `gpt-oss-120b`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["CEREBRAS_API_KEY"],   # never hard-code the key
    base_url="https://api.cerebras.ai/v1",
)

response = client.chat.completions.create(
    model="llama-3.3-70b",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.CEREBRAS_API_KEY, // never hard-code the key
  baseURL: "https://api.cerebras.ai/v1",
});

const response = await client.chat.completions.create({
  model: "llama-3.3-70b",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://api.cerebras.ai/v1/chat/completions" \
  -H "Authorization: Bearer $CEREBRAS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama-3.3-70b",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Yes.** Cerebras publishes an OpenAI-compatibility guide: point the `openai` SDK at the Cerebras base URL and key. A first-party `cerebras-cloud-sdk` is also available.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Interactive assistants where time-to-first-token dominates
- Reasoning or agent loops with many sequential model calls
- Speed benchmarking against other open-model hosts

## Limitations

- Smaller catalogue, concentrated on selected open-weight models
- Free-tier context length and concurrency are capped

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [Quickstart](https://inference-docs.cerebras.ai/quickstart)
- [OpenAI compatibility](https://inference-docs.cerebras.ai/resources/openai)
- [Rate limits](https://inference-docs.cerebras.ai/support/rate-limits)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
