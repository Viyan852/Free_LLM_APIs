# Hugging Face Inference Providers

A router in front of many third-party inference providers, letting you call models hosted on the Hub through one OpenAI-compatible endpoint and one token.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | Hugging Face Inference Providers |
| Website | <https://huggingface.co/> |
| API documentation | <https://huggingface.co/docs/inference-providers> |
| Base URL (OpenAI-compatible) | `https://router.huggingface.co/v1` |
| Environment variable | `HF_TOKEN` |
| OpenAI-compatible | Yes |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://huggingface.co/>
- API documentation: <https://huggingface.co/docs/inference-providers>
- API keys / console: <https://huggingface.co/settings/tokens>

## Free-tier information

Hugging Face includes a small monthly credit allowance for Inference Providers on free accounts, with a larger allowance for PRO and paid plans. You can also attach your own provider keys and be billed by that provider.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Create a User Access Token with inference permissions and send it as `Authorization: Bearer YOUR_API_KEY`.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export HF_TOKEN="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://router.huggingface.co/v1
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `openai/gpt-oss-120b`
- `meta-llama/Llama-3.3-70B-Instruct`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["HF_TOKEN"],   # never hard-code the key
    base_url="https://router.huggingface.co/v1",
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
  apiKey: process.env.HF_TOKEN, // never hard-code the key
  baseURL: "https://router.huggingface.co/v1",
});

const response = await client.chat.completions.create({
  model: "openai/gpt-oss-120b",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://router.huggingface.co/v1/chat/completions" \
  -H "Authorization: Bearer $HF_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "openai/gpt-oss-120b",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Yes.** The router exposes `/v1/chat/completions`. A model ID can optionally be suffixed with `:provider` (for example `openai/gpt-oss-120b:cerebras`) to pin a specific upstream provider instead of using automatic selection.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Reaching many open models, including niche community models, with one token
- Comparing the same open model across upstream providers
- Prototyping directly from Hub model pages

## Limitations

- Included credits on free accounts are small — designed for exploration
- Latency, context length, and feature support vary by upstream provider

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [Inference Providers overview](https://huggingface.co/docs/inference-providers/index)
- [Chat completion task](https://huggingface.co/docs/inference-providers/tasks/chat-completion)
- [Pricing and billing](https://huggingface.co/docs/inference-providers/pricing)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
