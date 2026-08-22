# Free AI Models Registry

Machine-readable registry of AI API providers that currently offer ongoing free access, plus clearly marked prototype/trial providers.

The canonical source is [`registry.json`](./registry.json). No API keys or secrets are stored in this repository.

## Default `free-models` pool

SimpleAIProxy should include only models where:

- the provider is enabled,
- `free_tier.default_group_eligible` is `true`,
- the model is enabled,
- `include_in_default_group` is `true`, and
- the environment variable named by `api_key_env` is present locally.

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
| 90 | OpenRouter | `openrouter/free` | dynamic free router |

## Tracked but not automatically routed

### Cloudflare Workers AI

Cloudflare Workers AI has a genuine ongoing free allocation of **10,000 neurons per day** on Workers Free. Cloudflare explicitly confirms that several strong models remain available on the Free plan, including:

- `@cf/google/gemma-4-26b-a4b-it`
- `@cf/zai-org/glm-4.7-flash`
- `@cf/nvidia/nemotron-3-120b-a12b`

Workers AI exposes an OpenAI-compatible `/v1/chat/completions` endpoint, but its URL contains the user's Cloudflare account ID. It is therefore tracked in `registry.json` with `default_group_eligible: false` until SimpleAIProxy's registry integration supports provider URL templating / `CLOUDFLARE_ACCOUNT_ID` in addition to an API key.

Cloudflare moved `@cf/moonshotai/kimi-k2.6`, `@cf/moonshotai/kimi-k2.7-code`, and `@cf/zai-org/glm-5.2` to the Workers Paid plan on July 28, 2026, so those models must not be treated as free-tier models.

### NVIDIA NIM

NVIDIA NIM hosted endpoints are tracked for discovery but are excluded from the default group because NVIDIA describes the hosted access under API Trial / prototype terms rather than an ongoing production free tier.

## Environment variables

SimpleAIProxy resolves credentials locally using the `api_key_env` values in the registry. Typical variables are:

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
- `chat_completions_url` — OpenAI-compatible chat-completions endpoint or documented endpoint template when the provider requires non-secret account metadata
- `api_mode` — currently `openai_chat_completions`
- `api_key_env` — name of the local environment variable containing the credential
- `free_tier.type` — `ongoing`, `prototype_trial`, etc.
- `free_tier.default_group_eligible` — whether the provider may enter the automatic `free-models` pool
- `sources` — official documentation used to verify the entry
- `models[]` — model IDs and routing metadata

Providers that require additional non-secret local configuration may include fields such as `account_id_env`. Such providers stay out of the automatic pool until the consuming proxy supports those fields.

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
3. Keep prototype/trial access clearly separated from the default pool.
4. Preserve provider/model IDs exactly as required by the upstream API.
5. Update `updated_at` whenever verified availability, limits, endpoints, or model IDs change.
6. Never commit API keys, OAuth tokens, account identifiers, or other secrets.

Last verified: **2026-08-22**.
