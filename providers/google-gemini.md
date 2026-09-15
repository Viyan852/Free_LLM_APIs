# Google Gemini API

Google's hosted Gemini models, offered through the Gemini API in Google AI Studio with both a native REST/SDK interface and an OpenAI-compatible surface.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | Google Gemini API |
| Website | <https://ai.google.dev/> |
| API documentation | <https://ai.google.dev/gemini-api/docs> |
| Base URL (OpenAI-compatible) | `https://generativelanguage.googleapis.com/v1beta/openai/` |
| Base URL (native API) | `https://generativelanguage.googleapis.com/v1beta` |
| Environment variable | `GEMINI_API_KEY` |
| OpenAI-compatible | Yes |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://ai.google.dev/>
- API documentation: <https://ai.google.dev/gemini-api/docs>
- API keys / console: <https://aistudio.google.com/apikey>

## Free-tier information

Google documents a free tier for the Gemini API in Google AI Studio, with separate (higher) limits on paid tiers. Availability and eligibility can depend on region and account type.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Create an API key in Google AI Studio. The native API accepts the key in the `x-goog-api-key` header; the OpenAI-compatible endpoint accepts it as a normal `Authorization: Bearer` token.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export GEMINI_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://generativelanguage.googleapis.com/v1beta/openai/
```

Native (non-OpenAI-shaped) API:

```text
https://generativelanguage.googleapis.com/v1beta
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `gemini-2.5-flash`
- `gemini-2.5-pro`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["GEMINI_API_KEY"],   # never hard-code the key
    base_url="https://generativelanguage.googleapis.com/v1beta/openai/",
)

response = client.chat.completions.create(
    model="gemini-2.5-flash",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.GEMINI_API_KEY, // never hard-code the key
  baseURL: "https://generativelanguage.googleapis.com/v1beta/openai/",
});

const response = await client.chat.completions.create({
  model: "gemini-2.5-flash",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://generativelanguage.googleapis.com/v1beta/openai/chat/completions" \
  -H "Authorization: Bearer $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemini-2.5-flash",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Yes.** Google publishes an OpenAI-compatibility layer for chat completions, embeddings, and streaming. Some Gemini-specific features are only available through the native API or the `google-genai` SDK.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Long-context document and codebase analysis
- Multimodal input (text plus images, audio, or video) where supported
- Prototyping in Google AI Studio before moving to production

## Limitations

- Free-tier requests may be used to improve Google's products — review the terms before sending sensitive data
- Model availability and feature support differ between the native and OpenAI-compatible APIs

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [Gemini API quickstart](https://ai.google.dev/gemini-api/docs/quickstart)
- [OpenAI compatibility](https://ai.google.dev/gemini-api/docs/openai)
- [Rate limits](https://ai.google.dev/gemini-api/docs/rate-limits)
- [Pricing and tiers](https://ai.google.dev/gemini-api/docs/pricing)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
