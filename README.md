# Free AI Models Registry

Machine-readable registry of AI API providers that currently offer ongoing free access, plus clearly marked prototype/trial providers.

The canonical source is [`registry.json`](./registry.json). No API keys or secrets are stored in this repository.

## Default `free-models` pool

SimpleAIProxy should include only models where:

- the provider is enabled,
- `free_tier.default_group_eligible` is `true`,
- the model is enabled,
- `include_in_default_group` is `true`, and
- the required local provider credential is complete.

For normal providers that means an API key. Account-scoped providers such as Cloudflare Workers AI additionally require the account ID named by `account_id_env`; SimpleAIProxy can store that value separately for every named credential.

Current default candidates are ordered by `priority` (lower is preferred):

| Priority | Provider | Model | Free access |
|---:|---|---|---|
| 10 | Google Gemini | `gemini-3.7-flash` | ongoing free tier |
| 20 | Groq | `openai/gpt-oss-120b` | ongoing free plan |
| 30 | Mistral AI | `mistral-large-latest` | Free mode |
| 40 | SambaNova Cloud | `gpt-oss-120b` | free tier |
| 50 | Groq | `qwen/qwen3.6-27b` | ongoing free plan |
| 60 | SambaNova Cloud | `DeepSeek-V3.1` | free tier |
| 70 | Google Gemini | `gemini-3.6-flash` | ongoing free tier |
| 75 | Cloudflare Workers AI | `@cf/zai-org/glm-4.7-flash` | Workers Free, shared 10k neurons/day |
| 80 | Cloudflare Workers AI | `@cf/google/gemma-4-26b-a4b-it` | Workers Free, shared 10k neurons/day |
| 85 | Cloudflare Workers AI | `@cf/nvidia/nemotron-3-120b-a12b` | Workers Free, shared 10k neurons/day |
| 90 | OpenRouter | `openrouter/free` | dynamic free router |
| 95 | NVIDIA NIM | `z-ai/glm-5.2` | free prototype/development endpoint |
| 100 | NVIDIA NIM | `nvidia/nemotron-3-ultra-550b-a55b` | free prototype/development endpoint |
| 105 | NVIDIA NIM | `nvidia/nemotron-3.5-lightning-30b-a3b` | free prototype/development endpoint |

## Provider notes

### Cloudflare Workers AI

Cloudflare Workers AI has a genuine ongoing free allocation of **10,000 neurons per day** on Workers Free. Cloudflare explicitly confirms that several strong models remain available on the Free plan, including:

- `@cf/google/gemma-4-26b-a4b-it`
- `@cf/zai-org/glm-4.7-flash`
- `@cf/nvidia/nemotron-3-120b-a12b`

Workers AI exposes an OpenAI-compatible `/v1/chat/completions` endpoint. Its URL includes the user's Cloudflare Account ID, so the registry declares `account_id_env: CLOUDFLARE_ACCOUNT_ID` and uses `{CLOUDFLARE_ACCOUNT_ID}` in the endpoint template. SimpleAIProxy resolves this value from either environment variables or from the account ID saved beside each UI-managed credential.

Cloudflare moved `@cf/moonshotai/kimi-k2.6`, `@cf/moonshotai/kimi-k2.7-code`, and `@cf/zai-org/glm-5.2` to the Workers Paid plan on July 28, 2026, so those models are not treated as Free-plan models here.

### NVIDIA NIM

NVIDIA Build currently exposes several models as **Free Endpoint** / free serverless APIs for development. These endpoints are still governed by NVIDIA API Trial/prototype terms rather than an ordinary production free tier. They are deliberately kept classified as `prototype_trial` while being explicitly enabled as late fallbacks in this registry's `free-models` pool.

## Environment variables

SimpleAIProxy resolves credentials locally using the registry metadata. Typical variables are:

```bash
GEMINI_API_KEY=...
GROQ_API_KEY=...
MISTRAL_API_KEY=...
SAMBANOVA_API_KEY=...
OPENROUTER_API_KEY=...
CLOUDFLARE_API_KEY=...
CLOUDFLARE_ACCOUNT_ID=...
NVIDIA_API_KEY=...
```

The registry never contains those values.

## Registry contract

Each provider supplies:

- `id` — stable provider identifier
- `chat_completions_url` — OpenAI-compatible chat-completions endpoint or endpoint template
- `api_mode` — currently `openai_chat_completions`
- `api_key_env` — name of the local environment variable containing the API credential
- `account_id_env` — optional environment variable whose value resolves the same-named `{PLACEHOLDER}` in the endpoint URL; SimpleAIProxy may also store it per named credential
- `free_tier.type` — `ongoing`, `prototype_trial`, etc.
- `free_tier.default_group_eligible` — whether the provider may enter the automatic `free-models` pool
- `sources` — official documentation used to verify the entry
- `models[]` — model IDs and routing metadata

Each model can provide:

- `enabled`
- `include_in_default_group`
- `priority`
- `status`
- `context_tokens`
- `capabilities`
- `free_limits`
- `notes`

## Maintenance rules

When updating this registry:

1. Prefer official provider documentation.
2. Do not classify signup credits or short-lived promotional credits as an ongoing free tier.
3. Keep prototype/trial access clearly labeled even when it is intentionally allowed into the fallback pool.
4. Preserve provider/model IDs exactly as required by the upstream API.
5. Update `updated_at` whenever verified availability, limits, endpoints, or model IDs change.
6. Never commit API keys, OAuth tokens, account identifiers, or other secrets.

Last verified: **2026-08-22**.
