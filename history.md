# Codex Project History

## Cycle 2 — 2026-05-21: Custom Persona Subagents

### Problem
Persona skills such as product manager, product designer, architect, reviewer, data scientist, historian, and UI tester were available as slash-command skills, but they did not define independent subagent model, reasoning, or sandbox defaults.

### Changes Applied
- Added global custom subagents under `~/.codex/agents`
- Mirrored the same subagents under `~/.codex-app/agents` for Codex Desktop compatibility
- Created persona subagents: `product_manager`, `product_designer`, `technical_architect`, `code_reviewer`, `data_scientist`, `project_historian`, `ui_tester`
- Created operational subagents: `explorer`, `docs_researcher`, `builder`
- Left `security-best-practices` as a skill-only workflow because it has detailed reference-loading behavior and should trigger only for explicit security requests
- Installed Lazyweb design skills from `aboul3ata/lazyweb-skill` into both `~/.codex/skills` and `~/.codex-app/skills`
- Updated `product_manager` to explicitly review PRDs, roadmaps, product briefs, backlog/issues, and project history before giving scope verdicts
- Updated `product_designer` to use Lazyweb and Playwright when relevant, produce high-fidelity mockup artifacts, save them under `.design/product-designer/{topic}-{date}/`, and create `builder-handoff.md` for the next builder/coder
- Updated `ui_tester` to explicitly use the `$playwright` skill and wrapper-script workflow for browser automation, snapshots, and artifacts under `output/playwright/`
- Restored structured handoff tags for `product_manager`, `technical_architect`, and `code_reviewer`
- Narrowed `product_designer` writes to `.design/product-designer/{topic}-{date}/` unless implementation work is explicitly assigned
- Set `ui_tester` to `workspace-write` and limited ordinary writes to `output/playwright/`
- Kept `data_scientist` advisory-only with read-only sandbox
- Changed `builder` to `gpt-5.4` with `medium` reasoning
- Removed the `code-reviewer` skill package from `~/.codex-app/skills` so reviewer routing uses the `code_reviewer` subagent only
- Removed duplicate persona skill packages from `~/.codex-app/skills`: `data-scientist`, `product-designer`, `product-manager`, `project-historian`, `technical-architect`, and `ui-tester`
- Removed the same duplicate persona skill packages from Windows `C:\Users\patpp\.codex\skills`
- Mirrored the custom subagent TOML files into Windows `C:\Users\patpp\.codex\agents` so Windows and WSL Codex configs have matching subagents

### Model Defaults
| Agent | Model | Reasoning | Sandbox |
|-------|-------|-----------|---------|
| `explorer` | `gpt-5.4-mini` | `medium` | `read-only` |
| `docs_researcher` | `gpt-5.4-mini` | `medium` | `read-only` |
| `builder` | `gpt-5.4` | `medium` | inherited |
| `product_manager` | `gpt-5.5` | `xhigh` | `read-only` |
| `product_designer` | `gpt-5.5` | `high` | `workspace-write` |
| `technical_architect` | `gpt-5.5` | `xhigh` | `read-only` |
| `code_reviewer` | `gpt-5.4` | `high` | `read-only` |
| `data_scientist` | `gpt-5.4` | `high` | `read-only` |
| `project_historian` | `gpt-5.4-mini` | `medium` | `workspace-write` |
| `ui_tester` | `gpt-5.4` | `high` | `workspace-write` |

### Verification
- Parsed all TOML files in both agent directories with Python `tomllib`
- Confirmed `security_best_practices.toml` is absent from both agent directories
- Confirmed duplicate persona skill folders are absent across WSL `~/.codex`, WSL `~/.codex-app`, Windows `C:\Users\patpp\.codex`, and checked project-local Codex roots

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
