# Z.ai (GLM)

Z.ai's open platform serves the GLM family of models through an OpenAI-compatible API, including coding-oriented variants.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | Z.ai (GLM) |
| Website | <https://z.ai/> |
| API documentation | <https://docs.z.ai/> |
| Base URL (OpenAI-compatible) | `https://api.z.ai/api/paas/v4` |
| Environment variable | `ZAI_API_KEY` |
| OpenAI-compatible | Yes |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://z.ai/>
- API documentation: <https://docs.z.ai/>
- API keys / console: <https://z.ai/model-api>

## Free-tier information

Z.ai has offered free or promotional access to selected GLM models and flash variants; availability changes and some models are paid only.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Register on the Z.ai open platform, create an API key, and send it as `Authorization: Bearer YOUR_API_KEY`.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export ZAI_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://api.z.ai/api/paas/v4
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `glm-4.5`
- `glm-4.5-air`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["ZAI_API_KEY"],   # never hard-code the key
    base_url="https://api.z.ai/api/paas/v4",
)

response = client.chat.completions.create(
    model="glm-4.5",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.ZAI_API_KEY, // never hard-code the key
  baseURL: "https://api.z.ai/api/paas/v4",
});

const response = await client.chat.completions.create({
  model: "glm-4.5",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://api.z.ai/api/paas/v4/chat/completions" \
  -H "Authorization: Bearer $ZAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "glm-4.5",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Yes.** Z.ai's documentation shows the `openai` Python client configured with the `https://api.z.ai/api/paas/v4/` base URL.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Coding assistants and agentic coding workflows
- Chinese and English bilingual applications
- Cost-sensitive access to competitive open models

## Limitations

- Which models are free versus paid changes frequently
- Some documentation and support material is China-region oriented

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [API introduction](https://docs.z.ai/api-reference/introduction)
- [Quick start](https://docs.z.ai/guides/overview/quick-start)
- [HTTP API calls](https://docs.z.ai/guides/develop/http/introduction)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
