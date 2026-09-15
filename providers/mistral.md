# Mistral AI (La Plateforme)

Mistral's hosted API for its own open-weight and commercial models, with chat, embeddings, vision, and function calling.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | Mistral AI (La Plateforme) |
| Website | <https://mistral.ai/> |
| API documentation | <https://docs.mistral.ai/> |
| Base URL (OpenAI-compatible) | `https://api.mistral.ai/v1` |
| Environment variable | `MISTRAL_API_KEY` |
| OpenAI-compatible | Mostly |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://mistral.ai/>
- API documentation: <https://docs.mistral.ai/>
- API keys / console: <https://console.mistral.ai/api-keys>

## Free-tier information

Mistral documents a free experimentation tier on La Plateforme that requires account activation, with separate limits from paid usage.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Create an API key in the Mistral console and send it as `Authorization: Bearer YOUR_API_KEY`.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export MISTRAL_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://api.mistral.ai/v1
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `mistral-small-latest`
- `mistral-large-latest`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["MISTRAL_API_KEY"],   # never hard-code the key
    base_url="https://api.mistral.ai/v1",
)

response = client.chat.completions.create(
    model="mistral-small-latest",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.MISTRAL_API_KEY, // never hard-code the key
  baseURL: "https://api.mistral.ai/v1",
});

const response = await client.chat.completions.create({
  model: "mistral-small-latest",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://api.mistral.ai/v1/chat/completions" \
  -H "Authorization: Bearer $MISTRAL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "mistral-small-latest",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Mostly.** The chat completions endpoint follows the OpenAI request/response shape closely and works with the `openai` SDKs, but Mistral also ships its own `mistralai` clients that expose provider-specific features first.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- European hosting and data-residency requirements
- Function calling and structured output in a well-documented API
- Mixed open-weight and commercial model usage under one key

## Limitations

- Free tier is intended for experimentation, not production traffic
- Some features (for example agents or document processing) are outside the OpenAI-compatible surface

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [Quickstart](https://docs.mistral.ai/getting-started/quickstart/)
- [Models overview](https://docs.mistral.ai/getting-started/models/models_overview/)
- [Rate limits and tiers](https://docs.mistral.ai/deployment/laplateforme/tier/)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
