# JavaScript guide

Node.js and TypeScript patterns for OpenAI-compatible providers.

## Installation

```bash
npm init -y
npm install openai
npm install --save-dev typescript @types/node   # optional, for TypeScript
```

Use ESM (`"type": "module"` in `package.json`) so top-level `await` works, and Node 18+ so
`fetch` and `process.loadEnvFile` are available.

## Environment variables

```bash
# .env  (never commit this file)
GROQ_API_KEY=YOUR_API_KEY
```

```javascript
// Node 20.6+ can load a .env file without a dependency:
//   node --env-file=.env app.js
const apiKey = process.env.GROQ_API_KEY;
if (!apiKey) {
  throw new Error("GROQ_API_KEY is not set");
}
```

> **Never ship a provider key to the browser.** Anything in client-side JavaScript is public.
> Call the provider from a server route and forward only the result.

## Basic request

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.GROQ_API_KEY,
  baseURL: "https://api.groq.com/openai/v1",
  timeout: 30_000,
  maxRetries: 2,
});

const response = await client.chat.completions.create({
  model: "llama-3.3-70b-versatile",
  messages: [
    { role: "system", content: "You are concise and precise." },
    { role: "user", content: "What is idempotency?" },
  ],
  temperature: 0.3,
  max_tokens: 300,
});

console.log(response.choices[0].message.content);
```

## Streaming

```javascript
const stream = await client.chat.completions.create({
  model: "llama-3.3-70b-versatile",
  messages: [{ role: "user", content: "Write a haiku about caching." }],
  stream: true,
});

for await (const chunk of stream) {
  const delta = chunk.choices[0]?.delta?.content;
  if (delta) process.stdout.write(delta);
}
process.stdout.write("\n");
```

Streaming to a browser from a server route:

```javascript
// Web-standard handler (Next.js route handler, Hono, Deno, Cloudflare Workers, ...)
export async function POST(request) {
  const { prompt } = await request.json();

  const upstream = await client.chat.completions.create({
    model: "llama-3.3-70b-versatile",
    messages: [{ role: "user", content: prompt }],
    stream: true,
  });

  const encoder = new TextEncoder();
  const body = new ReadableStream({
    async start(controller) {
      try {
        for await (const chunk of upstream) {
          const delta = chunk.choices[0]?.delta?.content;
          if (delta) controller.enqueue(encoder.encode(delta));
        }
      } finally {
        controller.close();
      }
    },
  });

  return new Response(body, {
    headers: { "Content-Type": "text/plain; charset=utf-8" },
  });
}
```

## Error handling

```javascript
import OpenAI from "openai";

const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

async function ask(prompt, attempts = 4) {
  for (let attempt = 0; attempt < attempts; attempt += 1) {
    try {
      const response = await client.chat.completions.create({
        model: "llama-3.3-70b-versatile",
        messages: [{ role: "user", content: prompt }],
      });
      return response.choices[0].message.content ?? "";
    } catch (error) {
      const status = error instanceof OpenAI.APIError ? error.status : undefined;

      if (status === 401) throw new Error("Invalid API key — check your environment variable.");
      if (status === 400) throw new Error(`Bad request (model ID or parameters): ${error.message}`);

      const retryable = status === 429 || (status !== undefined && status >= 500) || status === undefined;
      if (!retryable || attempt === attempts - 1) throw error;

      const retryAfter = Number(error?.headers?.["retry-after"]);
      const waitMs = Number.isFinite(retryAfter)
        ? retryAfter * 1000
        : 2 ** attempt * 1000 + Math.random() * 500;
      console.warn(`retrying in ${Math.round(waitMs)}ms (status ${status ?? "network"})`);
      await sleep(waitMs);
    }
  }
  throw new Error("unreachable");
}
```

Also handle aborts so a cancelled browser request does not keep burning quota:

```javascript
const controller = new AbortController();
setTimeout(() => controller.abort(), 20_000);

await client.chat.completions.create(
  { model: "llama-3.3-70b-versatile", messages: [{ role: "user", content: "Hi" }] },
  { signal: controller.signal },
);
```

## Security

- Keys live on the server only — never in `NEXT_PUBLIC_*`, Vite `import.meta.env` client vars, or bundled config.
- Put your own rate limiting and auth in front of any proxy route, or your free quota becomes someone else's.
- Validate and cap user input length before forwarding it.
- Escape or sanitise model output before inserting it into the DOM; never `eval` it.
- Commit `.env.example` with placeholder values, never the real `.env`.
- Rotate keys immediately if one lands in a public bundle or a log.

## Provider switching

```javascript
// Model IDs are examples — check each provider's docs for current models.
const PROVIDERS = {
  groq: { baseURL: "https://api.groq.com/openai/v1", env: "GROQ_API_KEY", model: "llama-3.3-70b-versatile" },
  cerebras: { baseURL: "https://api.cerebras.ai/v1", env: "CEREBRAS_API_KEY", model: "llama-3.3-70b" },
  openrouter: { baseURL: "https://openrouter.ai/api/v1", env: "OPENROUTER_API_KEY", model: "openai/gpt-oss-120b" },
};

function clientFor(name) {
  const config = PROVIDERS[name];
  return new OpenAI({ apiKey: process.env[config.env], baseURL: config.baseURL });
}
```

## See also

- [How OpenAI-compatible APIs work](openai-compatible-apis.md)
- [cURL guide](curl.md)
