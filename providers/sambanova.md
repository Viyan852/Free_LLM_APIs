# SambaNova Cloud

SambaCloud serves open-weight models on SambaNova's RDU hardware through an OpenAI-compatible API.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | SambaNova Cloud |
| Website | <https://sambanova.ai/> |
| API documentation | <https://docs.sambanova.ai/> |
| Base URL (OpenAI-compatible) | `https://api.sambanova.ai/v1` |
| Environment variable | `SAMBANOVA_API_KEY` |
| OpenAI-compatible | Yes |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://sambanova.ai/>
- API documentation: <https://docs.sambanova.ai/>
- API keys / console: <https://cloud.sambanova.ai/apis>

## Free-tier information

SambaNova provides free developer access to SambaCloud for evaluation, with rate limits that differ from paid plans.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Generate an API key in the SambaCloud portal and send it as `Authorization: Bearer YOUR_API_KEY`. Keys cannot be viewed again after creation.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export SAMBANOVA_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://api.sambanova.ai/v1
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `Meta-Llama-3.3-70B-Instruct`
- `Llama-4-Maverick-17B-128E-Instruct`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["SAMBANOVA_API_KEY"],   # never hard-code the key
    base_url="https://api.sambanova.ai/v1",
)

response = client.chat.completions.create(
    model="Meta-Llama-3.3-70B-Instruct",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.SAMBANOVA_API_KEY, // never hard-code the key
  baseURL: "https://api.sambanova.ai/v1",
});

const response = await client.chat.completions.create({
  model: "Meta-Llama-3.3-70B-Instruct",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://api.sambanova.ai/v1/chat/completions" \
  -H "Authorization: Bearer $SAMBANOVA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Meta-Llama-3.3-70B-Instruct",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Yes.** The base URL is documented for use with the OpenAI clients, including streaming responses.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Fast inference on large open-weight models
- Evaluating alternative accelerator hardware
- Drop-in replacement testing for an existing OpenAI integration

## Limitations

- Model IDs are case-sensitive and provider-specific
- Free developer access is evaluation-oriented

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [API keys and URLs](https://docs.sambanova.ai/docs/en/get-started/api-keys-urls)
- [Quickstart](https://docs.sambanova.ai/docs/en/get-started/quickstart)
- [Supported models](https://docs.sambanova.ai/docs/en/capabilities/supported-models)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
