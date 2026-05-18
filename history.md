# Codex Project History

## Cycle 1 — 2026-05-15: Codex Desktop WSL+Ollama Configuration Fix

### Problem
Codex Desktop app on Windows (using WSL terminal) had two issues:
1. Couldn't connect to Ollama from WSL context — WSL2's localhost (`127.0.0.1:11434`) doesn't forward to the Windows-hosted Ollama service reliably.
2. Model picker showed only "Custom" with intelligence levels instead of actual model names — missing `model_catalog_json` configuration and a cached empty `models_cache.json`.

### Root Causes Identified
| Cause | Config | Issue |
|-------|--------|-------|
| Wrong Ollama base URL | WSL `~/.codex-app/config.toml` | Used `127.0.0.1:11434` — WSL2 can't reach Windows localhost; needs gateway IP `172.31.0.1` |
| Missing model catalog | WSL config | No `model_catalog_json` entry pointing to `ollama-launch-models.json` |
| Unnecessary `env_key` | Both WSL & Windows configs | `env_key = "OLLAMA_API_KEY"` on `ollama2` provider causes auth errors (Ollama doesn't require API keys) |
| Cross-wired gateway IP | Windows `C:\Users\patpp\.codex\config.toml` | `ollama2` provider had WSL gateway IP `172.31.0.1` instead of `127.0.0.1` |
| Trailing slash inconsistency | Both configs | Some URLs had trailing slashes, others didn't |

### Changes Applied
- **WSL config** (`~/.codex-app/config.toml`): Fixed `base_url` and `openai_base_url` to `172.31.0.1:11434/v1/`, added `model_catalog_json` path, removed `env_key`, removed Windows sandbox section
- **Windows config** (`C:\Users\patpp\.codex\config.toml`): Fixed `ollama2` provider URL from `172.31.0.1:11434/v1` to `127.0.0.1:11434/v1/`, removed `env_key`, restored from backup
- **Model catalog**: Restored `/mnt/c/Users/patpp/.codex/ollama-launch-models.json` from backup (18 models)
- **Cache**: Deleted both `models_cache.json` files (WSL and Windows) to force regeneration

### Review Findings
- `localhostForwarding=true` already exists in `.wslconfig` but doesn't reliably forward Ollama's port
- `172.31.0.1` gateway IP is the correct workaround for WSL→Windows host communication
- Model catalog JSON is valid with 18 models
- Full Codex Desktop restart (from Windows Start menu) required to regenerate `models_cache.json`

### Outstanding Items
- User needs to fully restart Codex Desktop from Windows Start menu to populate the model picker
- If model picker still shows only "Custom", repeat the full restart cycle

### Files Modified
- `~/.codex-app/config.toml` (WSL)
- `C:\Users\patpp\.codex\config.toml` (Windows)
- `/mnt/c/Users/patpp/.codex/ollama-launch-models.json` (restored from backup)
