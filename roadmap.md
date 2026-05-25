# Codex Project Roadmap

## Status: Cycle 2 Complete

### ✅ Completed (2026-05-15)
- **WSL+Ollama connectivity fix**: Resolved WSL→Windows Ollama communication by switching from `127.0.0.1` to gateway IP `172.31.0.1`
- **Model picker fix**: Added `model_catalog_json` to WSL config and restored model catalog file (18 models)
- **Config hygiene**: Removed unnecessary `env_key`, normalized trailing slashes, fixed cross-wired IPs between WSL and Windows configs
- **Cache cleanup**: Deleted stale `models_cache.json` files

### ✅ Completed (2026-05-21)
- **Custom subagents**: Added mirrored custom agent definitions under `~/.codex/agents` and `~/.codex-app/agents`
- **Persona migration**: Added subagents for product management, product design, architecture, code review, data science, project history, and UI testing
- **Operational agents**: Added `explorer`, `docs_researcher`, and `builder`
- **Security skill retained**: Kept `security-best-practices` as a skill-only workflow rather than a subagent
- **Design workflow upgraded**: Installed Lazyweb design skills from `aboul3ata/lazyweb-skill`; product designer now writes high-fidelity mockups and builder handoff docs
- **PM workflow upgraded**: Product manager now explicitly reviews PRDs, roadmaps, briefs, issues, and project history before scope decisions
- **UI testing workflow upgraded**: UI tester now explicitly follows the `$playwright` skill and wrapper CLI workflow
- **Subagent scope tightened**: Restored PM/architect/reviewer structured handoff tags, narrowed designer/tester write scopes, kept data scientist advisory-only, and changed builder to `gpt-5.4 medium`
- **Reviewer routing simplified**: Removed the `code-reviewer` skill so the `code_reviewer` subagent is the only reviewer entry point
- **Persona routing simplified**: Removed duplicate persona skills so product, design, architecture, data science, historian, reviewer, and UI testing roles route through subagents
- **Windows/WSL parity**: Removed duplicate persona skills from Windows `.codex` and mirrored custom subagents into Windows `.codex/agents`

### 🔧 Known Issues / Technical Debt
| Issue | Severity | Status |
|-------|----------|--------|
| `localhostForwarding=true` in `.wslconfig` doesn't reliably forward Ollama port | Low | Workaround in place (gateway IP) |
| Codex Desktop needs manual full restart to regenerate `models_cache.json` | Low | Documented in history |
| Codex Desktop may need a restart before newly mirrored subagents appear in UI/runtime menus | Low | Expected after config additions |

### 📋 Pending / Future
- [ ] **Validate model picker**: After user restarts Codex Desktop, verify the model picker shows model names instead of just "Custom"
- [ ] **Consider automating `.wslconfig` localhost forwarding**: Investigate if a WSL init script or Docker-style port forwarding can make `127.0.0.1:11434` work reliably in WSL2
- [ ] **Config parity check**: Audit whether any other provider configs have similar IP/slash/env_key inconsistencies
- [ ] **Validate subagent visibility**: Restart Codex Desktop and confirm the custom subagents are discoverable
