# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

zh-TW 在地化**全部完成**。本批（F，收官）在地化最後一份使用者導向文件 `skills/brainstorming/visual-companion.md`。所有面向終端中文使用者的內容皆已在地化。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（每批合併後從最新 `main` 重開）
- Base: `main`
- Previous delivery: PR #1–#18 全部合併。

## 本批範圍（F：visual-companion 在地化，收官）

- `skills/brainstorming/visual-companion.md`（292 行）——brainstorming 的視覺陪伴功能指南（何時用瀏覽器 vs 終端機、server 啟動、迴圈、內容片段撰寫、CSS class、事件格式、設計提示、清理）。

## 本批不變式（自查已過）

- **16 個 fenced 區塊全部 byte-identical**：所有 ` ```bash `／` ```html `／` ```jsonl ` 範例（含 CSS class、HTML 標記、bash 指令、JSON、frame 模板片段、註解）working-tree vs `HEAD` 逐字相同。
- **逐字保留**（皆在 inline code 內受保護）：所有 flag（`--project-dir`、`--open`、`--host`、`--url-host`、`--foreground`、`--idle-timeout-minutes`）、路徑／變數（`screen_dir`、`state_dir`、`$STATE_DIR/events`、`.superpowers/brainstorm/`、`/tmp`）、script（`scripts/start-server.sh`、`scripts/stop-server.sh`、`scripts/frame-template.html`、`scripts/helper.js`）、CSS class（`.subtitle`、`data-multiselect` 等）、env（`CODEX_CI`）、平台名（Claude Code、Codex、Copilot CLI）、`run_in_background: true`、`mode: "async"`。
- **標點**：散文全形（逗號/冒號/括號/問號/驚嘆號），漢字相鄰半形殘餘＝0,inline code 內零全形漏入。

## 完整盤點（收官）

- **已在地化（全部面向使用者的內容）**:14 支 `SKILL.md`＋行為鏈 reference 子檔＋`visual-companion.md`＋typography＋README／安裝＋移植指南＋Mermaid 對照頁＋全部 i18n meta 文件。
- **刻意留英**:`anthropic-best-practices.md`（1150 行，外部方法論）、工具對應 reference（`references/*-tools.md`，與 harness 工具名一致）、測試 fixture／`CREATION-LOG.md`、subagent prompt 檔（`spec-document-reviewer-prompt.md`、`implementer-prompt.md`，行為敏感）、所有 ` ```dot `／`.js`。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- canonical 的 fenced 程式碼（HTML/CSS/bash/JSON 範例、` ```dot ` 流程圖）逐字保留、零改動。

## Resume Target

**zh-TW 在地化專案大功告成。**面向終端中文使用者的所有文件皆已在地化並合併（PR #1–#18＋本批 F）。

剩餘皆為刻意留英或環境限制，非缺口：
- 刻意留英項目見上「完整盤點」。
- 行為 eval（`behavior-test-cases.md`）需 tmux 環境且 `evals/` 未 clone，無法在本 session 執行。durable 已知限制。

## Backlog（皆為可選，無 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆（`baseline`、`harness`、`checkpoint` 等裸英文）。術語一致性，可另批。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-18
- Task: visual-companion.md 在地化（收官批）
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支
