# Cloudflare Workers AI

Inference on Cloudflare's global network, callable from Workers bindings or over REST, with a catalogue of open models identified by `@cf/...` IDs.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | Cloudflare Workers AI |
| Website | <https://developers.cloudflare.com/workers-ai/> |
| API documentation | <https://developers.cloudflare.com/workers-ai/> |
| Base URL (OpenAI-compatible) | `https://api.cloudflare.com/client/v4/accounts/YOUR_ACCOUNT_ID/ai/v1` |
| Base URL (native API) | `https://api.cloudflare.com/client/v4/accounts/YOUR_ACCOUNT_ID/ai/run/{model}` |
| Environment variable | `CLOUDFLARE_API_TOKEN` |
| OpenAI-compatible | Yes |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://developers.cloudflare.com/workers-ai/>
- API documentation: <https://developers.cloudflare.com/workers-ai/>
- API keys / console: <https://dash.cloudflare.com/profile/api-tokens>

## Free-tier information

Cloudflare documents a daily allocation of Workers AI usage on the free plan, measured in neurons, with paid usage beyond it.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Create a scoped API token with Workers AI permissions and send it as `Authorization: Bearer YOUR_API_KEY`. REST calls also need your Cloudflare account ID in the URL path.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export CLOUDFLARE_API_TOKEN="YOUR_API_KEY"
export CLOUDFLARE_ACCOUNT_ID="YOUR_VALUE"   # your Cloudflare account ID
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://api.cloudflare.com/client/v4/accounts/YOUR_ACCOUNT_ID/ai/v1
```

Native (non-OpenAI-shaped) API:

```text
https://api.cloudflare.com/client/v4/accounts/YOUR_ACCOUNT_ID/ai/run/{model}
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `@cf/meta/llama-3.1-8b-instruct`
- `@cf/openai/gpt-oss-120b`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["CLOUDFLARE_API_TOKEN"],   # never hard-code the key
    base_url="https://api.cloudflare.com/client/v4/accounts/YOUR_ACCOUNT_ID/ai/v1",
)

response = client.chat.completions.create(
    model="@cf/meta/llama-3.1-8b-instruct",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.CLOUDFLARE_API_TOKEN, // never hard-code the key
  baseURL: "https://api.cloudflare.com/client/v4/accounts/YOUR_ACCOUNT_ID/ai/v1",
});

const response = await client.chat.completions.create({
  model: "@cf/meta/llama-3.1-8b-instruct",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://api.cloudflare.com/client/v4/accounts/YOUR_ACCOUNT_ID/ai/v1/chat/completions" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "@cf/meta/llama-3.1-8b-instruct",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Yes.** Workers AI provides OpenAI-compatible `/v1/chat/completions` and `/v1/embeddings` endpoints in addition to its native `/ai/run/{model}` route.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Adding inference to an existing Cloudflare Workers or Pages application
- Edge-side pre-processing, moderation, or embeddings close to users
- Serverless deployments with no separate inference vendor to manage

## Limitations

- Model catalogue is Cloudflare-curated and uses provider-specific `@cf/` model IDs
- REST usage requires an account ID as well as a token

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [Workers AI REST API](https://developers.cloudflare.com/workers-ai/get-started/rest-api/)
- [OpenAI-compatible endpoints](https://developers.cloudflare.com/workers-ai/configuration/open-ai-compatibility/)
- [Models catalogue](https://developers.cloudflare.com/workers-ai/models/)
- [Limits and pricing](https://developers.cloudflare.com/workers-ai/platform/pricing/)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
