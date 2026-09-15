# Choosing an API

There is no single best free LLM API. Pick by constraint, and keep a second provider wired up
so an outage or a quota reset does not stop your project.

> All specifics — quotas, context windows, model availability — must be confirmed with the
> provider. **Check the provider's official documentation for current limits.**

## Decision order

1. **Hard constraints first** — data residency, commercial-use terms, geographic availability.
2. **Capability** — does any model there do the task at all (vision? tools? long context?).
3. **Quota** — is the free allowance enough for your actual traffic?
4. **Speed** — does latency affect the experience?
5. **Portability** — how hard is it to leave later?

## Speed

- Two different numbers matter: **time to first token** (how responsive it feels) and **tokens per second** (how fast a long answer completes).
- Specialised inference hardware (for example [Groq](../providers/groq.md), [Cerebras](../providers/cerebras.md), [SambaNova](../providers/sambanova.md)) generally targets both.
- Router APIs add a hop; a routed request is rarely the fastest option.
- Streaming makes almost any provider feel faster — enable it before optimising vendors.
- Benchmark from your own deployment region with your own prompts. Marketing numbers use ideal conditions.

## Free quota

- Compare shapes, not just size: requests per minute, tokens per minute, requests per day, and monthly credits are different constraints.
- Ask what happens at the limit: hard `429`, silent degradation, or automatic paid overflow.
- Included credits (for example [Hugging Face](../providers/huggingface.md), [NVIDIA NIM](../providers/nvidia-nim.md)) are exhaustible; recurring per-minute limits reset.
- Trial keys (for example [Cohere](../providers/cohere.md)) are often contractually evaluation-only.

## Models

- Open-weight catalogues dominate free tiers; if you need a specific frontier closed model, a router such as [OpenRouter](../providers/openrouter.md) is usually the only free-ish route.
- Query `GET /v1/models` rather than trusting any list, including this repository's.
- Expect churn: models are deprecated with little notice. Keep the model ID in configuration, not scattered through code.

## Context length

- Match the window to the job: chat needs little, document and codebase analysis needs a lot.
- Free tiers sometimes cap context below the model's maximum.
- Remember output tokens share the budget with input on many models.
- Long context is often slower and more expensive per request — retrieval plus a small window frequently beats a giant window.

## Tool calling

- Support is uneven across providers even for the same model, and quality varies more than presence.
- Test multi-step, multi-tool, and parallel calls, not just a single happy path.
- Verify the provider returns `tool_calls` in the OpenAI shape if you rely on that structure.

## Vision and multimodality

- Confirm which specific models accept images, and whether the provider takes URLs, base64, or both.
- Audio and video input are rarer; [Gemini](../providers/google-gemini.md) is the broadest option here among free tiers.
- Image *generation* is a different product from chat — check separately.

## Streaming

- Nearly universal for chat completions, but details differ: `usage` may be absent, and some providers do not stream tool calls.
- Verify your whole path streams end to end — a buffering proxy or serverless platform can silently break it.

## Reliability

- Free tiers are best-effort. Assume outages and shed load gracefully.
- Look for a public status page and a changelog.
- Design for failure: timeouts, exponential backoff with jitter, a fallback provider, and cached or degraded responses.
- Consider whether a router's built-in failover is worth the extra latency hop.

## Geographic availability

- Some providers restrict sign-up or serving by country; some free tiers are region-limited.
- Latency is dominated by distance — check where inference actually runs, not where the company is.
- For EU data-residency requirements, start with [Mistral](../providers/mistral.md); for edge-local execution, [Cloudflare Workers AI](../providers/cloudflare-workers-ai.md).

## Commercial usage

- Read the terms before shipping. Free tiers frequently prohibit production or resale, and evaluation keys almost always do.
- Check whether your prompts may be used for training — this is common on free tiers and often unacceptable for customer data.
- Confirm the *model* licence too, not just the API terms, if you redistribute outputs.
- Understand the paid upgrade path before you depend on the free one.

## Quick shortlist

| Priority | Try first | Then |
| --- | --- | --- |
| Speed | [Groq](../providers/groq.md) | [Cerebras](../providers/cerebras.md), [SambaNova](../providers/sambanova.md) |
| Model variety | [OpenRouter](../providers/openrouter.md) | [Hugging Face](../providers/huggingface.md) |
| Long context / multimodal | [Gemini](../providers/google-gemini.md) | [Mistral](../providers/mistral.md) |
| RAG and embeddings | [Cohere](../providers/cohere.md) | [Mistral](../providers/mistral.md) |
| Edge apps | [Cloudflare Workers AI](../providers/cloudflare-workers-ai.md) | — |
| Future self-hosting | [NVIDIA NIM](../providers/nvidia-nim.md) | — |
| Coding agents | [Z.ai](../providers/zai.md) | [Groq](../providers/groq.md) |

## Anti-patterns

- Hard-coding one provider's base URL and model deep in application code.
- Treating a free tier as a service-level agreement.
- Sending regulated or customer data to a free tier without reading the data-use terms.
- Choosing on a benchmark table instead of your own evaluation set.
- Skipping retries and backoff, then blaming the provider for `429`s.

## See also

- [How OpenAI-compatible APIs work](openai-compatible-apis.md)
- [Python guide](python.md) · [JavaScript guide](javascript.md) · [cURL guide](curl.md)
