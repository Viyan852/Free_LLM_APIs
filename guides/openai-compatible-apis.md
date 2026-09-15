# How OpenAI-compatible APIs work

Most of the providers in this repository speak the same HTTP dialect as OpenAI's
`/v1/chat/completions` endpoint. That means one client library, one request shape, and
exactly **three things to change** when you switch providers.

## The three knobs

| Knob | What it is | Example |
| --- | --- | --- |
| **API key** | A provider-specific secret, read from an environment variable | `GROQ_API_KEY` |
| **Base URL** | The provider's OpenAI-compatible root, ending before `/chat/completions` | `https://api.groq.com/openai/v1` |
| **Model** | A provider-specific model ID | `llama-3.3-70b-versatile` |

Everything else — `messages`, `temperature`, `stream`, `tools`, and the response shape with
`choices[0].message.content` — usually stays the same.

## Why it works

The OpenAI chat-completions format became the de facto standard, so providers implement it to
remove switching friction. Under the hood they may run entirely different models and
hardware, but the JSON contract is shared:

```json
{
  "model": "MODEL_ID",
  "messages": [{ "role": "user", "content": "Hello" }],
  "temperature": 0.7,
  "stream": false
}
```

## Minimal Python example

```python
import os

from openai import OpenAI  # pip install openai

client = OpenAI(
    api_key=os.environ["GROQ_API_KEY"],
    base_url="https://api.groq.com/openai/v1",
)

response = client.chat.completions.create(
    model="llama-3.3-70b-versatile",
    messages=[{"role": "user", "content": "Say hello in one short sentence."}],
)
print(response.choices[0].message.content)
```

## Switching providers with a config table

```python
import os

from openai import OpenAI

# Model IDs are examples only — check each provider's docs for current models.
PROVIDERS = {
    "groq": {
        "base_url": "https://api.groq.com/openai/v1",
        "env": "GROQ_API_KEY",
        "model": "llama-3.3-70b-versatile",
    },
    "cerebras": {
        "base_url": "https://api.cerebras.ai/v1",
        "env": "CEREBRAS_API_KEY",
        "model": "llama-3.3-70b",
    },
    "openrouter": {
        "base_url": "https://openrouter.ai/api/v1",
        "env": "OPENROUTER_API_KEY",
        "model": "openai/gpt-oss-120b",
    },
    "gemini": {
        "base_url": "https://generativelanguage.googleapis.com/v1beta/openai/",
        "env": "GEMINI_API_KEY",
        "model": "gemini-2.5-flash",
    },
}


def ask(provider: str, prompt: str) -> str:
    config = PROVIDERS[provider]
    client = OpenAI(
        api_key=os.environ[config["env"]],
        base_url=config["base_url"],
    )
    response = client.chat.completions.create(
        model=config["model"],
        messages=[{"role": "user", "content": prompt}],
    )
    return response.choices[0].message.content


if __name__ == "__main__":
    print(ask("groq", "Give me one fact about the Voyager probes."))
```

## Adding a fallback

Because the interface is shared, failover is a loop:

```python
def ask_with_fallback(prompt: str, order=("groq", "cerebras", "openrouter")) -> str:
    last_error: Exception | None = None
    for provider in order:
        try:
            return ask(provider, prompt)
        except Exception as exc:  # narrow this in production code
            last_error = exc
            print(f"{provider} failed: {exc}")
    raise RuntimeError("all providers failed") from last_error
```

## What is *not* portable

- **Model IDs.** Always provider-specific.
- **Context windows and max output tokens.** Differ per model.
- **Tool calling, JSON mode, vision, and reasoning controls.** Support is uneven; feature-detect rather than assume.
- **Token accounting and `usage` fields.** May be missing or shaped differently.
- **Rate-limit headers and error bodies.** Similar in spirit, different in detail.
- **Non-chat endpoints** such as rerank, image, or audio APIs, which are often native-only.

## Checklist before switching

1. Confirm the provider documents an OpenAI-compatible base URL.
2. Read its model list endpoint or docs for a valid model ID.
3. Re-test streaming, tool calling, and structured output — these break first.
4. Re-tune prompts: a different model needs different prompting even with identical code.
5. Keep per-provider keys in separate environment variables so you can rotate them independently.

## See also

- [Python guide](python.md)
- [JavaScript guide](javascript.md)
- [cURL guide](curl.md)
- [Choosing an API](choosing-an-api.md)
