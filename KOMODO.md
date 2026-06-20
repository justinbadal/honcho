# Komodo Deployment

This deployment is intended for a fork such as `justinbadal/honcho` and uses
Komodo's Stack UI to build from source. Secrets stay in Komodo, not in Git.

## Komodo Secret

Create one secret in Komodo:

- `HONCHO_BIFROST_API_KEY`: your Bifrost API key

## Stack Settings

Create a Stack with these settings:

- Name: `honcho`
- Source: Git repo
- Repo: `justinbadal/honcho`
- Branch: `main`
- File paths: `docker-compose.komodo.yml`
- Project name: `honcho`
- Extra args: `--build --pull always`
- Auto Update: off
- Poll for Updates: off

Komodo image auto-update is not useful here because this stack builds Honcho
from source instead of tracking a published Honcho image tag. To update, deploy
the Stack again after pulling changes into the fork.

## Stack Environment

Paste this into the Stack Environment field:

```env
LOG_LEVEL=INFO
AUTH_USE_AUTH=false

HONCHO_API_HOST=127.0.0.1
HONCHO_API_HOST_PORT=8000
HONCHO_DB_HOST=127.0.0.1
HONCHO_DB_HOST_PORT=15432
HONCHO_VALKEY_HOST=127.0.0.1
HONCHO_VALKEY_HOST_PORT=16379

LLM_OPENAI_API_KEY=[[HONCHO_BIFROST_API_KEY]]

CACHE_ENABLED=true
CACHE_URL=redis://valkey:6379/0?suppress=true

EMBEDDING_VECTOR_DIMENSIONS=1024
EMBEDDING_MAX_INPUT_TOKENS=8192
EMBEDDING_MODEL_CONFIG__TRANSPORT=openai
EMBEDDING_MODEL_CONFIG__MODEL=ER/jina-embeddings-v3
EMBEDDING_MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1

DERIVER_MODEL_CONFIG__TRANSPORT=openai
DERIVER_MODEL_CONFIG__MODEL=vllm/openai/gpt-oss-20b
DERIVER_MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1

SUMMARY_MODEL_CONFIG__TRANSPORT=openai
SUMMARY_MODEL_CONFIG__MODEL=vllm/openai/gpt-oss-20b
SUMMARY_MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1

DREAM_DEDUCTION_MODEL_CONFIG__TRANSPORT=openai
DREAM_DEDUCTION_MODEL_CONFIG__MODEL=vllm/openai/gpt-oss-20b
DREAM_DEDUCTION_MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1
DREAM_INDUCTION_MODEL_CONFIG__TRANSPORT=openai
DREAM_INDUCTION_MODEL_CONFIG__MODEL=vllm/openai/gpt-oss-20b
DREAM_INDUCTION_MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1

DIALECTIC_LEVELS__minimal__MODEL_CONFIG__TRANSPORT=openai
DIALECTIC_LEVELS__minimal__MODEL_CONFIG__MODEL=vllm/openai/gpt-oss-20b
DIALECTIC_LEVELS__minimal__MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1
DIALECTIC_LEVELS__low__MODEL_CONFIG__TRANSPORT=openai
DIALECTIC_LEVELS__low__MODEL_CONFIG__MODEL=vllm/openai/gpt-oss-20b
DIALECTIC_LEVELS__low__MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1
DIALECTIC_LEVELS__medium__MODEL_CONFIG__TRANSPORT=openai
DIALECTIC_LEVELS__medium__MODEL_CONFIG__MODEL=vllm/openai/gpt-oss-20b
DIALECTIC_LEVELS__medium__MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1
DIALECTIC_LEVELS__high__MODEL_CONFIG__TRANSPORT=openai
DIALECTIC_LEVELS__high__MODEL_CONFIG__MODEL=vllm/openai/gpt-oss-20b
DIALECTIC_LEVELS__high__MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1
DIALECTIC_LEVELS__max__MODEL_CONFIG__TRANSPORT=openai
DIALECTIC_LEVELS__max__MODEL_CONFIG__MODEL=vllm/openai/gpt-oss-20b
DIALECTIC_LEVELS__max__MODEL_CONFIG__OVERRIDES__BASE_URL=https://bifrost.servrar.net/v1
```

## Pangolin

Point Pangolin at `http://127.0.0.1:8000` when it runs on the same host as the
Komodo stack. If you change `HONCHO_API_HOST_PORT`, update the Pangolin
upstream to match. The compose file binds published ports to localhost by
default.

## Checks

After deploying, watch the `api` logs. Startup should run database migrations,
run `scripts/configure_embeddings.py --yes`, then start FastAPI. Confirm:

```bash
curl http://127.0.0.1:8000/health
```
