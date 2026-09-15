# Free LLM APIs

![Markdown lint](https://github.com/OWNER/free-llm-apis/actions/workflows/markdown-check.yml/badge.svg)
![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)
![Docs only](https://img.shields.io/badge/content-documentation-blue.svg)
![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)

A practical, source-linked directory of large language model APIs that offer a free tier,
free credits, or free evaluation access — plus copy-pasteable Python, JavaScript, and cURL
examples for each one.

This repository is **documentation only**. There is no SDK to install, no telemetry, and no
API keys anywhere in the tree.

> [!IMPORTANT]
> Quotas, model names, endpoints, and rate limits change constantly. This repository
> deliberately does **not** publish specific quota numbers. For every provider:
> **Check the provider's official documentation for current limits.**

## Provider table

| Provider | Free access | OpenAI-compatible | Best for |
| --- | --- | --- | --- |
| [Gemini](providers/google-gemini.md) | Yes (see page) | Yes | Long context and multimodal work |
| [Groq](providers/groq.md) | Yes (see page) | Yes | Raw inference speed |
| [OpenRouter](providers/openrouter.md) | Yes (see page) | Yes | Trying many models behind one key |
| [Workers AI](providers/cloudflare-workers-ai.md) | Yes (see page) | Yes | Edge and Workers deployments |
| [Mistral](providers/mistral.md) | Yes (see page) | Mostly | EU-hosted general-purpose models |
| [Cohere](providers/cohere.md) | Yes (see page) | Via compatibility API | RAG, embeddings, and reranking |
| [Hugging Face](providers/huggingface.md) | Yes (see page) | Yes | Breadth of open models |
| [NVIDIA NIM](providers/nvidia-nim.md) | Yes (see page) | Yes | Cloud-to-self-host migration paths |
| [Cerebras](providers/cerebras.md) | Yes (see page) | Yes | Lowest latency open models |
| [SambaNova](providers/sambanova.md) | Yes (see page) | Yes | Fast large open-weight models |
| [GitHub Models](providers/github-models.md) | Retired | n/a | Nothing new — retired |
| [Z.ai](providers/zai.md) | Yes (see page) | Yes | Coding-focused GLM models |

"Free access" means the provider documents some form of no-cost tier, trial key, or
included credits — not that it is free forever or free at any volume.

## Recommended providers by use case

| If you need... | Start with | Why |
| --- | --- | --- |
| The fastest possible responses | [Groq](providers/groq.md), [Cerebras](providers/cerebras.md) | Purpose-built inference hardware, OpenAI-compatible |
| To try many models with one key | [OpenRouter](providers/openrouter.md), [Hugging Face](providers/huggingface.md) | Router APIs across many upstream providers |
| Long context or multimodal input | [Google Gemini](providers/google-gemini.md) | Large context windows and image/audio/video support |
| RAG, embeddings, and reranking | [Cohere](providers/cohere.md) | Dedicated embed and rerank endpoints |
| Inference inside an edge app | [Cloudflare Workers AI](providers/cloudflare-workers-ai.md) | Runs on Cloudflare's network next to your Worker |
| EU-hosted models | [Mistral](providers/mistral.md) | European provider with a documented free tier |
| A path to self-hosting later | [NVIDIA NIM](providers/nvidia-nim.md) | Same containers run in the cloud and on your GPUs |
| Large open-weight models, fast | [SambaNova](providers/sambanova.md) | Open models on custom accelerators |
| Coding-focused assistants | [Z.ai](providers/zai.md) | GLM models tuned for coding and agents |

## Providers

- [Google Gemini API](providers/google-gemini.md) — Google's hosted Gemini models, offered through the Gemini API in Google AI Studio with both a native REST/SDK interface and an OpenAI-compatible surface.
- [Groq](providers/groq.md) — Groq serves open-weight models on its own LPU inference hardware and is known for very high token throughput on chat completions.
- [OpenRouter](providers/openrouter.md) — A single OpenAI-compatible gateway that routes requests to many upstream model providers, including a rotating set of free-to-use model variants.
- [Cloudflare Workers AI](providers/cloudflare-workers-ai.md) — Inference on Cloudflare's global network, callable from Workers bindings or over REST, with a catalogue of open models identified by `@cf/.
- [Mistral AI (La Plateforme)](providers/mistral.md) — Mistral's hosted API for its own open-weight and commercial models, with chat, embeddings, vision, and function calling.
- [Cohere](providers/cohere.md) — Enterprise-oriented models for retrieval-augmented generation, embeddings, and reranking, plus a Command family of chat models.
- [Hugging Face Inference Providers](providers/huggingface.md) — A router in front of many third-party inference providers, letting you call models hosted on the Hub through one OpenAI-compatible endpoint and one token.
- [NVIDIA NIM (build.nvidia.com)](providers/nvidia-nim.md) — NVIDIA-hosted NIM microservices expose OpenAI-compatible endpoints for a wide catalogue of open models, with the same containers available for self-hosting.
- [Cerebras Inference](providers/cerebras.md) — Inference on Cerebras wafer-scale hardware, focused on very low latency for open-weight chat models.
- [SambaNova Cloud](providers/sambanova.md) — SambaCloud serves open-weight models on SambaNova's RDU hardware through an OpenAI-compatible API.
- [GitHub Models](providers/github-models.md) — A GitHub-hosted model playground and inference API that let developers call models with a GitHub personal access token.
- [Z.ai (GLM)](providers/zai.md) — Z.

## Guides

- [How OpenAI-compatible APIs work](guides/openai-compatible-apis.md) — swap providers by changing three things
- [Python guide](guides/python.md) — install, env vars, streaming, error handling
- [JavaScript guide](guides/javascript.md) — npm, env vars, streaming, error handling
- [cURL guide](guides/curl.md) — raw HTTP, status codes, debugging
- [Choosing an API](guides/choosing-an-api.md) — a decision framework

## Repository structure

```text
free-llm-apis/
├── README.md
├── CONTRIBUTING.md
├── LICENSE
├── .gitignore
├── providers/            # one page per provider
├── guides/               # language and concept guides
├── templates/
│   └── provider-template.md
└── .github/
    └── workflows/
        └── markdown-check.yml
```

## Security guidance

- **Never commit keys.** Use environment variables or a secret manager; `.env` is git-ignored here.
- **Never put a key in front-end code.** Browser and mobile clients cannot keep secrets — proxy through a backend.
- **Treat free tiers as non-private by default.** Some providers may use free-tier traffic to improve their models. Read the terms before sending personal, customer, or proprietary data.
- **Scope and rotate keys.** Prefer least-privilege tokens, rotate on any suspected exposure, and delete unused keys.
- **Set spending limits** where the provider supports them, so a mistake stays cheap.
- **Validate model output** before executing it, rendering it as HTML, or passing it to a shell or database.

## Contributing

Corrections and new providers are welcome. Start with [CONTRIBUTING.md](CONTRIBUTING.md) and
copy [templates/provider-template.md](templates/provider-template.md) for a new provider page.
Please link an official source for every factual claim, and avoid pasting quota numbers that
will be stale within weeks.

## Disclaimer

This project is community-maintained and not affiliated with, endorsed by, or sponsored by any
provider listed. Free tiers, quotas, model names, endpoints, and rate limits change often and vary by account, region, and plan. Nothing in this repository is a guarantee of availability. Always confirm details in the provider's official documentation before relying on them. Pages are marked with a "last verified" date; treat anything
older than that date as unconfirmed.

## License

[MIT](LICENSE) — documentation and examples.
