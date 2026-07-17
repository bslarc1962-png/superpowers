# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

核心 skill 全數在地化已完成並合併（PR #1–#13）。收尾：typography sweep（#14）、README／安裝說明（#15）皆已合併。本批（C）在地化開發者移植指南 `docs/porting-to-a-new-harness.md`。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（每批合併後從最新 `main` 重開）
- Base: `main`
- Previous delivery: PR #1–#15 全部合併。

## 本批範圍（C：移植指南在地化）

- `docs/porting-to-a-new-harness.md`（827 行）——把 Superpowers 移植到新 harness 的完整開發者指南（Part 1–8＋Appendix A/B、三種整合形態 Shape A/B/C、能力檢查表、bootstrap 注入、工具對應、tmux 驗收流程、發布與跨平台）。

## 本批不變式（自查已過）

- **fenced 區塊 byte-identical**：唯一的 ```bash tmux 區塊（含英文註解）working-tree vs `HEAD` 逐字相同。
- **逐字保留**：所有檔案路徑、env var（`${CLAUDE_PLUGIN_ROOT}` 等）、JSON 片段（`{ "additional_context": "…" }` 等）、matcher 字串（`startup|clear|compact`）、issue 引用（#750／#894／#1202）、工具/欄位名（`subagent_type`、`contextFileName`、`apply_patch`、`run-hook.cmd`）、control tag（`<EXTREMELY_IMPORTANT>`、`<EXTREMELY-IMPORTANT>`）、驗收 prompt（`Let's make a react todo list`）、`✔ context : ANTIGRAVITY.md`、產品/harness 名稱、`.version-bump.json` 等——皆在 inline code 內受保護。
- **章節識別碼保留**:`Part 1–8`、`Shape A/B/C`、`Appendix A/B`、`Step 1–7` 維持英文，散文中的交叉引用（如「見 Part 5」）與標題一致。
- **全形標點**：散文逗號/冒號/括號/問號/驚嘆號全形；漢字相鄰半形殘餘＝0；inline code 內零全形標點漏入。**分號維持半形**（比對 corpus:70 半 vs 12 全，半形為主流）。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 安裝命令、路徑、URL、程式碼、harness 名稱、章節識別碼逐字保留。
- 這是在地化，非改動專案內容或 rebrand。

## Resume Target

面向終端中文使用者的所有文件皆已在地化：核心 skill＋行為鏈 reference（#1–#13）＋typography（#14）＋README/安裝說明（#15）＋移植指南（本批）。

DOT 流程圖決定：**canonical ```dot 一律不動**（保留 render-graphs.js 工具、writing-skills 慣例、上游 fidelity、執行時行為）；另建 Mermaid 對照頁供 GitHub inline review——**下一批（D）**。8 支 skill 有 ```dot：subagent-driven-development、writing-skills、test-driven-development、systematic-debugging（root-cause-tracing、condition-based-waiting）、dispatching-parallel-agents、brainstorming。

## Backlog（非本批 blocker）

- **批次 D**：Mermaid 對照圖庫頁（`docs/i18n/` 下），把上述 8 張 ```dot 流程圖以 Mermaid 重畫，GitHub inline 顯示。canonical skill 不動。
- glossary 首次出現 gloss 慣例執行寬鬆（`baseline`、`harness`、`checkpoint` 等裸英文）。術語一致性，可另批。
- `anthropic-best-practices.md`（1150 行）刻意未在地化、`.dot`／`.js` 程式碼不譯。
- 行為 eval（`behavior-test-cases.md`）仍需 tmux 環境，`evals/` 未 clone。durable 已知缺口。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 移植指南在地化（`docs/porting-to-a-new-harness.md`）
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支
