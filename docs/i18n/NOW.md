# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

zh-TW 在地化**全部完成**。目前沒有翻譯 blocker；下一個可選工作是把既有 Markdown 行為案例接成可由本機重跑的 executable behavior-equivalence eval。

- Task Mode: `test`
- 目前只做 Superpowers fork 內的 eval workflow。
- 不修改 ARSS。
- 不在 eval 任務中順手修改 skill 正文。

## Active Branch / PR

- Branch: `docs/i18n-behavior-eval-task`
- Base: `main`
- Previous delivery: PR #1–#19 全部合併，zh-TW 在地化收官。
- Current task document: `docs/i18n/behavior-eval-task.md`

## Current Scope

第一輪只驗證三個已被 ARSS 借鑑的 skill：

1. `test-driven-development`
2. `systematic-debugging`
3. `verification-before-completion`

執行順序由 `docs/i18n/behavior-eval-task.md` 定義。新的 agent 必須先完成 Phase 0 inventory，不得直接進入完整 eval 或修改 skill。

## Completed Localization Snapshot

- 已在地化：14 支 `SKILL.md`、行為鏈 reference、`visual-companion.md`、typography、README／安裝、移植指南、Mermaid 對照頁與 i18n meta 文件。
- 刻意留英：`anthropic-best-practices.md`、工具對應 reference、測試 fixture／`CREATION-LOG.md`、行為敏感 subagent prompt、所有 canonical code／dot／script 內容。
- Fork baseline：`2b64d350b4ad10db32fef382604cd59c98c7ccc7`。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不向 `obra/superpowers` 開 eval／翻譯 PR。
- canonical fenced 程式碼、命令、路徑、控制標籤保持不變。
- Eval finding 與 skill repair 分離：確認 regression 後另開單一問題的修正 PR。
- `evals/`、tmux、憑證、transcript 與 cache 的 Git 邊界必須先在 Phase 0 釐清。

## Resume Target

讀取：

1. `AGENTS.md`
2. `docs/i18n/agent-handoff.md`
3. 本文件
4. `docs/i18n/behavior-eval-task.md`

然後只執行 `behavior-eval-task.md` 的 **Phase 0 — Inventory Only**。

建議分工：Claude Code 作為 orchestrator／reviewer；Codex 執行機械盤點、來源比對與 fixture 初稿。

## Known Context

- `docs/i18n/behavior-test-cases.md` 已定義 A–E 測試層級與各 skill 案例。
- 先前 hosted session 無 tmux，且 `evals/` 未 clone，因此未執行行為 eval。
- ARSS 曾完成一次 Level A 靜態 review；其結果可作線索，但不能取代本 fork 的 executable evidence。

## Last Handoff

- Handled by: GPT-5.6 Thinking／OpenAI，透過 ChatGPT Web + GitHub connector 建立 durable task。
- Date: 2026-07-21
- Task: 將 zh-TW behavior-equivalence eval 從 ARSS 拆回 Superpowers fork，建立本機 Agent 可接手的任務入口。
- Required first read: `docs/i18n/behavior-eval-task.md`
