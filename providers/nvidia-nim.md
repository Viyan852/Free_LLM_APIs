# NVIDIA NIM (build.nvidia.com)

NVIDIA-hosted NIM microservices expose OpenAI-compatible endpoints for a wide catalogue of open models, with the same containers available for self-hosting.

> **Subject to change.** The details below are a starting point, not a specification. Free-tier availability, quotas, model names, and rate limits are changed by providers without notice.
>
> **Check the provider's official documentation for current limits.**

## Quick facts

| Item | Value |
| --- | --- |
| Provider | NVIDIA NIM (build.nvidia.com) |
| Website | <https://build.nvidia.com/> |
| API documentation | <https://docs.api.nvidia.com/nim/reference/llm-apis> |
| Base URL (OpenAI-compatible) | `https://integrate.api.nvidia.com/v1` |
| Environment variable | `NVIDIA_API_KEY` |
| OpenAI-compatible | Yes |
| Free access | Documented by the provider — Check the provider's official documentation for current limits. |
| Rate limits | Check the provider's official documentation for current limits. |
| Last verified | 2026-09-14 |

## Official website and documentation

- Website: <https://build.nvidia.com/>
- API documentation: <https://docs.api.nvidia.com/nim/reference/llm-apis>
- API keys / console: <https://build.nvidia.com/>

## Free-tier information

NVIDIA provides API credits for evaluating hosted NIM endpoints on build.nvidia.com after you sign in and generate a key.

Exact quotas, token allowances, and eligibility rules are **not reproduced here** because they change frequently. Check the provider's official documentation for current limits.

## Authentication

Generate an API key from a model page on build.nvidia.com and send it as `Authorization: Bearer YOUR_API_KEY`.

```bash
# store the key in your shell profile or a .env file that is git-ignored
export NVIDIA_API_KEY="YOUR_API_KEY"
```

> **Never commit API keys.** Load them from environment variables or a secret manager, and rotate any key that has been exposed.

## Base API URL

```text
https://integrate.api.nvidia.com/v1
```

## Example models

Model IDs below are examples that appear in the provider's own documentation or console. Catalogues change constantly — list models programmatically or check the docs instead of trusting this page.

- `meta/llama-3.3-70b-instruct`
- `nvidia/llama-3.1-nemotron-70b-instruct`

## Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["NVIDIA_API_KEY"],   # never hard-code the key
    base_url="https://integrate.api.nvidia.com/v1",
)

response = client.chat.completions.create(
    model="meta/llama-3.3-70b-instruct",
    messages=[{"role": "user", "content": "Explain rate limiting in two sentences."}],
)

print(response.choices[0].message.content)
```

## JavaScript example

```javascript
// npm install openai
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.NVIDIA_API_KEY, // never hard-code the key
  baseURL: "https://integrate.api.nvidia.com/v1",
});

const response = await client.chat.completions.create({
  model: "meta/llama-3.3-70b-instruct",
  messages: [{ role: "user", content: "Explain rate limiting in two sentences." }],
});

console.log(response.choices[0].message.content);
```

## cURL example

```bash
curl "https://integrate.api.nvidia.com/v1/chat/completions" \
  -H "Authorization: Bearer $NVIDIA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta/llama-3.3-70b-instruct",
    "messages": [
      { "role": "user", "content": "Explain rate limiting in two sentences." }
    ]
  }'
```

## OpenAI compatibility

**Status: Yes.** NIM LLM endpoints implement OpenAI-style `/v1/chat/completions` and `/v1/models`, so hosted evaluation code ports directly to a self-hosted NIM container by changing only the base URL.

See [the OpenAI-compatible APIs guide](../guides/openai-compatible-apis.md) for how to swap providers by changing only the key, base URL, and model.

## Good use cases

- Evaluating a model in the cloud before self-hosting the same NIM container
- Access to NVIDIA-tuned Nemotron and partner models
- Enterprise pipelines that will eventually run on-premises GPUs

## Limitations

- Hosted evaluation credits are finite and intended for testing
- Production use generally means deploying NIM containers on your own GPUs

## Rate limits

Check the provider's official documentation for current limits. Limits typically differ per model, per tier, and per region, and are enforced with HTTP `429` responses. Implement retries with exponential backoff and honour any `Retry-After` header.

## Official documentation links

- [LLM APIs reference](https://docs.api.nvidia.com/nim/reference/llm-apis)
- [NIM for LLMs documentation](https://docs.nvidia.com/nim/large-language-models/latest/index.html)
- [Model catalogue](https://build.nvidia.com/models)

---

_Last verified: 2026-09-14._ Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them.
