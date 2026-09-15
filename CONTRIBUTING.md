# Contributing

Thanks for helping keep this directory accurate. The value of this repository is that
readers can trust it, so accuracy matters more than volume.

## Ground rules

1. **Link an official source** for every factual claim — the provider's own docs, not a blog post.
2. **Do not publish quota numbers.** Instead write: `Check the provider's official documentation for current limits.`
3. **No API keys, tokens, or secrets** in any file, including in examples. Use `YOUR_API_KEY` and environment variables.
4. **No affiliate or referral links.**
5. **Update the "last verified" date** on any page you check or change.
6. **Keep examples runnable**: prefer the official `openai` SDK plus a base URL override, since most providers are OpenAI-compatible.

## Adding a provider

1. Confirm the provider documents a free tier, trial key, or included credits.
2. Copy `templates/provider-template.md` to `providers/<slug>.md`, using a lowercase, hyphenated slug.
3. Fill in every section. If you cannot verify something, say so explicitly rather than guessing.
4. Add a row to the provider table in `README.md`, and a "best for" entry if it fits a use case not already covered.
5. Open a pull request describing what you verified and when.

## Updating an existing provider

- State in the pull request what changed and which doc page you checked.
- If a provider has removed its free tier or retired its API, say so on the page rather than deleting it — stale tutorials still point people there.

## Style

- One `#` H1 per file, then `##` sections in the order used by the template.
- Fenced code blocks with a language tag (`python`, `javascript`, `bash`, `text`).
- Sentence-case headings, tables for structured facts, relative links between pages.
- Wrap bare URLs in angle brackets (`<https://example.com>`) or use Markdown links.

## Checks

Markdown is linted in CI by `.github/workflows/markdown-check.yml` on pushes to `main` and on
pull requests. You can run a lint locally with any markdownlint CLI, for example:

```bash
npx markdownlint-cli2 "**/*.md"
```

## Code of conduct

Be civil and concrete. Review comments should be about the documentation, not the person.
