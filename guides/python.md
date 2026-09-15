# Python guide

A single, portable pattern for calling any OpenAI-compatible provider from Python.

## Installation

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install openai               # official client, works with compatible providers
pip install python-dotenv        # optional: load a .env file in development
```

## Environment variables

Keep keys out of source code. Put them in your shell profile or a git-ignored `.env`:

```bash
# .env  (never commit this file)
GROQ_API_KEY=YOUR_API_KEY
GEMINI_API_KEY=YOUR_API_KEY
```

```python
import os

from dotenv import load_dotenv  # development convenience only

load_dotenv()

api_key = os.environ["GROQ_API_KEY"]  # KeyError early beats a confusing 401 later
```

In production, inject variables through your platform's secret manager instead of a file.

## Basic request

```python
import os

from openai import OpenAI

client = OpenAI(
    api_key=os.environ["GROQ_API_KEY"],
    base_url="https://api.groq.com/openai/v1",
    timeout=30.0,
    max_retries=2,
)

response = client.chat.completions.create(
    model="llama-3.3-70b-versatile",
    messages=[
        {"role": "system", "content": "You are concise and precise."},
        {"role": "user", "content": "What is idempotency?"},
    ],
    temperature=0.3,
    max_tokens=300,
)

print(response.choices[0].message.content)
if response.usage:
    print("tokens:", response.usage.total_tokens)
```

## Streaming

```python
stream = client.chat.completions.create(
    model="llama-3.3-70b-versatile",
    messages=[{"role": "user", "content": "Write a haiku about caching."}],
    stream=True,
)

for chunk in stream:
    delta = chunk.choices[0].delta.content
    if delta:
        print(delta, end="", flush=True)
print()
```

Notes:

- Always guard against `None` deltas — the first and last chunks often carry no text.
- Close or fully consume the stream so the connection is released.
- Some providers omit `usage` on streamed responses.

## Error handling

```python
import random
import time

from openai import (
    APIConnectionError,
    APIStatusError,
    APITimeoutError,
    AuthenticationError,
    BadRequestError,
    RateLimitError,
)


def ask(prompt: str, attempts: int = 4) -> str:
    for attempt in range(attempts):
        try:
            response = client.chat.completions.create(
                model="llama-3.3-70b-versatile",
                messages=[{"role": "user", "content": prompt}],
            )
            return response.choices[0].message.content or ""
        except (RateLimitError, APITimeoutError, APIConnectionError) as exc:
            if attempt == attempts - 1:
                raise
            backoff = 2 ** attempt + random.random()
            print(f"retrying in {backoff:.1f}s after {type(exc).__name__}")
            time.sleep(backoff)
        except AuthenticationError:
            raise SystemExit("Invalid or missing API key — check your environment variable.")
        except BadRequestError as exc:
            raise SystemExit(f"Request rejected (bad model ID or parameters): {exc}")
        except APIStatusError as exc:
            if 500 <= exc.status_code < 600 and attempt < attempts - 1:
                time.sleep(2 ** attempt)
                continue
            raise
    raise RuntimeError("unreachable")
```

Rules of thumb:

- Retry `429` and `5xx` with exponential backoff and jitter; honour `Retry-After` when present.
- Never retry `400` or `401` — the request or credential is wrong and will stay wrong.
- Set a timeout. Without one, a hung connection can stall your process.
- Log the status code and request ID, never the key.

## Security

- Load keys from the environment; never hard-code or log them.
- Add `.env` to `.gitignore` (already done in this repository).
- Use one key per application and environment so you can rotate narrowly.
- Do not send secrets, customer data, or regulated data to a free tier without reading the provider's data-use terms.
- Treat model output as untrusted input: validate before executing, rendering, or querying with it.
- Scan commits for secrets in CI (for example with a pre-commit secret scanner).

## Provider switching

```python
import os
from dataclasses import dataclass

from openai import OpenAI


@dataclass(frozen=True)
class Provider:
    name: str
    base_url: str
    env_var: str
    model: str


PROVIDERS = [
    Provider("groq", "https://api.groq.com/openai/v1", "GROQ_API_KEY", "llama-3.3-70b-versatile"),
    Provider("cerebras", "https://api.cerebras.ai/v1", "CEREBRAS_API_KEY", "llama-3.3-70b"),
    Provider("mistral", "https://api.mistral.ai/v1", "MISTRAL_API_KEY", "mistral-small-latest"),
]


def client_for(provider: Provider) -> OpenAI:
    key = os.environ.get(provider.env_var)
    if not key:
        raise RuntimeError(f"missing {provider.env_var}")
    return OpenAI(api_key=key, base_url=provider.base_url, timeout=30.0)


def ask_any(prompt: str) -> str:
    errors = []
    for provider in PROVIDERS:
        try:
            response = client_for(provider).chat.completions.create(
                model=provider.model,
                messages=[{"role": "user", "content": prompt}],
            )
            return response.choices[0].message.content or ""
        except Exception as exc:
            errors.append(f"{provider.name}: {exc}")
    raise RuntimeError("all providers failed:\n" + "\n".join(errors))
```

Model IDs above are illustrative. **Check the provider's official documentation for current
models and limits.**

## See also

- [How OpenAI-compatible APIs work](openai-compatible-apis.md)
- [Choosing an API](choosing-an-api.md)
