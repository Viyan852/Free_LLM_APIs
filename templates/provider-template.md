# Provider Name

<!--
Copy this file to providers/<slug>.md and replace every placeholder.
Rules:
  * Link an official source for each claim.
  * Do NOT invent quotas, prices, model names, or limits.
    If you cannot verify a limit, write:
    "Check the provider's official documentation for current limits."
  * Never include a real API key. Use YOUR_API_KEY.
  * Update the "last verified" date at the bottom.
-->

One or two sentences describing what this provider is and what makes it notable.

> **Subject to change.** The details below are a starting point, not a specification.
> Free-tier availability, quotas, model names, and rate limits are changed by providers
> without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | Provider Name |
| Website | <https://example.com> |
| API documentation | <https://docs.example.com> |
| Base URL (OpenAI-compatible) | `https://api.example.com/v1` |
| Base URL (native API) | `https://api.example.com/native` (delete if not applicable) |
| Environment variable | `EXAMPLE_API_KEY` |
| OpenAI-compatible | Yes / Mostly / Via compatibility API / No |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | YYYY-MM-DD |

## Official website and documentation

- Website: <https://example.com>
- API documentation: <https://docs.example.com>
- API keys / console: <https://console.example.com/keys>

## Free-tier information

Describe *qualitatively* what the provider documents: a free tier, trial key, included
credits, or promotional access. Do not copy numbers.

Check the provider's official documentation for current limits.

## Authentication

How to obtain a key and how to send it.

```bash
export EXAMPLE_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager.

## Base API URL

```text
https://api.example.com/v1
```

## Example models

Model IDs are examples only; catalogues change.

- `example-model-small`
- `example-model-large`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["EXAMPLE_API_KEY"],
    base_url="https://api.example.com/v1",
)

response = client.chat.completions.create(
    model="example-model-small",
    messages=[{"role": "user", "content": "Say hello in one short sentence."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.EXAMPLE_API_KEY,
  baseURL: "https://api.example.com/v1",
});

const response = await client.chat.completions.create({
  model: "example-model-small",
  messages: [{ role: "user", content: "Say hello in one short sentence." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://api.example.com/v1/chat/completions" \
  -H "Authorization: Bearer $EXAMPLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "example-model-small",
    "messages": [{ "role": "user", "content": "Say hello in one short sentence." }]
  }'
```

## OpenAI compatibility

**Status: Yes / Mostly / Via compatibility API / No.** Explain what works with the `openai`
SDKs and what requires the provider's native API.

## Good use cases

- Use case one
- Use case two
- Use case three

## Limitations

- Limitation one
- Limitation two

## Rate limits

Check the provider's official documentation for current limits. Note the error code used when
limits are hit (usually HTTP `429`) and whether a `Retry-After` header is returned.

## Official documentation links

- [Quickstart](https://docs.example.com/quickstart)
- [Models](https://docs.example.com/models)
- [Rate limits](https://docs.example.com/rate-limits)

---

_Last verified: YYYY-MM-DD._ Free tiers, quotas, model names, endpoints, and rate limits
change often and vary by account, region, and plan. Always confirm details in the provider's
official documentation.
