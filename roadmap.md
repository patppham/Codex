# Codex Project Roadmap

## Status: Cycle 1 Complete

### ✅ Completed (2026-05-15)
- **WSL+Ollama connectivity fix**: Resolved WSL→Windows Ollama communication by switching from `127.0.0.1` to gateway IP `172.31.0.1`
- **Model picker fix**: Added `model_catalog_json` to WSL config and restored model catalog file (18 models)
- **Config hygiene**: Removed unnecessary `env_key`, normalized trailing slashes, fixed cross-wired IPs between WSL and Windows configs
- **Cache cleanup**: Deleted stale `models_cache.json` files

### 🔧 Known Issues / Technical Debt
| Issue | Severity | Status |
|-------|----------|--------|
| `localhostForwarding=true` in `.wslconfig` doesn't reliably forward Ollama port | Low | Workaround in place (gateway IP) |
| Codex Desktop needs manual full restart to regenerate `models_cache.json` | Low | Documented in history |

### 📋 Pending / Future
- [ ] **Validate model picker**: After user restarts Codex Desktop, verify the model picker shows model names instead of just "Custom"
- [ ] **Consider automating `.wslconfig` localhost forwarding**: Investigate if a WSL init script or Docker-style port forwarding can make `127.0.0.1:11434` work reliably in WSL2
- [ ] **Config parity check**: Audit whether any other provider configs have similar IP/slash/env_key inconsistencies
