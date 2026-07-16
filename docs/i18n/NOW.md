# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

建立繁體中文化方法與第一批核心 skill 的語意驗證。

## Active Branch / PR

- Branch: `agent/zh-tw-localization-foundation`
- PR: `#1` — `i18n(zh-TW): establish localization workflow and translate core entry skills`
- Base: `main`
- PR state: Draft

## Current Scope

已完成第一版：

- `docs/i18n/README.md`
- `docs/i18n/translation-guide.md`
- `docs/i18n/glossary.md`
- `docs/i18n/roadmap.md`
- `docs/i18n/behavior-test-cases.md`
- `docs/i18n/agent-handoff.md`
- `skills/using-superpowers/SKILL.md`
- `skills/brainstorming/SKILL.md`

## Current Task

### Owner

Claude／Anthropic reviewer

### Task Mode

`review-only`

### Assignment

對 PR #1 的兩份 skill 翻譯進行獨立 semantic review：

- `skills/using-superpowers/SKILL.md`
- `skills/brainstorming/SKILL.md`

依 `docs/i18n/agent-handoff.md` 的 finding 格式，將問題留在 PR review 或 PR conversation。

### Required Checks

1. 強制語氣是否被弱化，例如 `MUST`、`Do NOT`、`ONLY`。
2. checklist 項目與順序是否完整。
3. gate、數量限制與 terminal state 是否等價。
4. skill ID、路徑、命令、程式碼與控制標籤是否保持原樣。
5. 中文是否自然易讀，但沒有順手重設計上游方法論。
6. 特別檢查上游刻意使用的 `human partner`、Red Flags 與 rationalization wording 是否因翻譯產生行為偏差。

### Deliverable

- 在 PR #1 留下一份分類 review。
- Blocker／major findings 需指出對應原文 invariant。
- 本輪不要直接修改檔案。
- 若沒有 blocker，也要明確寫出已檢查項目與剩餘風險。

## Boundaries

- 不翻譯下一批 skill。
- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化 PR 提交到上游。
- 不 merge PR #1。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。

## Resume Target

Claude review 完成後：

1. 主 agent 彙整 findings。
2. 使用者決定哪些 findings 可進入 `patch-approved`。
3. 修正並執行至少一輪中文壓力案例與英文回歸案例。
4. 第一批驗證通過後，再建立下一個 PR 處理 `writing-plans` 與 `using-git-worktrees`。

## Last Handoff

- Created by: ChatGPT / OpenAI lineage
- Date: 2026-07-17
- Next agent: Claude／Anthropic lineage
- Required first read: `docs/i18n/agent-handoff.md`