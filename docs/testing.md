# Testing Superpowers

Superpowers 有兩種截然不同的測試，各自放在自己的目錄裡：

- **`tests/`** —— 外掛的非 LLM 程式碼能不能正常運作？涵蓋 brainstorm-server JS、OpenCode 外掛載入、codex-plugin 同步與分析工具的 Bash + node + python 整合測試。
- **`evals/`** —— agent 在真實 LLM session 上的行為對不對？以 Python harness 驅動 Claude Code／Codex／Gemini CLI 的真實 tmux session，並由一個 LLM actor 與 verifier 判定 skill 是否被遵循。

## 外掛測試

位於 `tests/`。目前有：

- `tests/brainstorm-server/` —— brainstorm server JS 程式碼的 node 測試套件。
- `tests/opencode/` —— OpenCode 外掛載入、bootstrap 快取與工具註冊的 bash 測試。
- `tests/codex-plugin-sync/` —— bash 同步驗證。
- `tests/kimi/` —— Kimi 外掛 manifest 接線的 bash／Python 檢查。
- `tests/claude-code/test-helpers.sh`、`analyze-token-usage.py` —— 其餘 bash 測試所用的工具。
- `tests/claude-code/test-subagent-driven-development.sh` —— 「agent 能否描述 SDD」的測試（無 drill 對應版本；測的是描述回想，不是行為）。
- `tests/claude-code/test-subagent-driven-development-integration.sh` —— 加上 token 分析的擴充版 SDD 整合測試（drill 涵蓋 YAGNI 子集；bash 另加 commit 數、Claude Code 任務追蹤與 token 遙測的斷言）。
- `tests/claude-code/test-worktree-native-preference.sh` —— worktree skill 的 RED-GREEN-REFACTOR 驗證（drill 涵蓋 PRESSURE 階段；bash 另涵蓋 RED／GREEN 基準）。
- `tests/explicit-skill-requests/` —— drill 未涵蓋的 Haiku 專屬、多輪、以及以 skill 名稱提示的測試。

透過對應目錄的 `run-*.sh` 或 `npm test` 執行外掛測試。

## Skill 行為 eval

位於 `evals/`。Drill 是 harness;情境放在 `evals/scenarios/*.yaml`。設定方式見 `evals/README.md`。快速開始：

```bash
cd evals
uv sync --extra dev
export ANTHROPIC_API_KEY=sk-...
uv run drill run triggering-test-driven-development -b claude
```

Drill 情境很慢（每個 3–30 分鐘以上），而且跑的是真實 LLM session。它們目前不屬於 CI;自然的後續做法是分層模型（PR 上跑快速子集、夜間＋隨需跑完整掃描）。
