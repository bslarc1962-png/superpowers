# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

第一批核心 skill 已合併至 `main`；目前正在補齊跨 agent 交接機制，並準備獨立語意 review。

## Active Branch / PR

- Branch: `agent/zh-tw-localization-foundation`
- PR: `#2` — `docs(i18n): add durable multi-agent handoff workflow`
- Base: `main`
- PR state: Draft
- Previous delivery: PR `#1` 已合併，包含本地化基礎文件與第一批兩個 skill 翻譯

## Current Scope

已進入 `main`：

- `docs/i18n/translation-guide.md`
- `docs/i18n/glossary.md`
- `docs/i18n/roadmap.md`
- `docs/i18n/behavior-test-cases.md`
- `skills/using-superpowers/SKILL.md`
- `skills/brainstorming/SKILL.md`

PR #2 待合併：

- `docs/i18n/README.md` 的交接入口更新
- `docs/i18n/agent-handoff.md`
- `docs/i18n/NOW.md`

## Current Task

### Owner

Claude／Anthropic reviewer

### Task Mode

`review-only`

### Assignment

執行兩部分獨立 review：

1. Review PR #2 的交接設計，確認新 agent 能否只靠 repo 入口理解目前任務、權限與交付位置。
2. Review 已由 PR #1 合併至 `main` 的兩份 skill 翻譯：
   - `skills/using-superpowers/SKILL.md`
   - `skills/brainstorming/SKILL.md`

依 `docs/i18n/agent-handoff.md` 的 finding 格式，把所有結果集中留在 PR #2 conversation。不要在已關閉的 PR #1 繼續分散新討論。

### Required Checks

#### 交接機制

1. 暖機順序是否足以讓沒有聊天背景的 agent 接手。
2. `review-only` 等 task mode 是否能清楚限制權限。
3. `NOW.md` 是否只保存短期狀態，沒有重複整份長期規則。
4. 是否仍需要使用者重述大量背景；若需要，指出缺少哪個 durable context。

#### Skill 語意

1. 強制語氣是否被弱化，例如 `MUST`、`Do NOT`、`ONLY`。
2. checklist 項目與順序是否完整。
3. gate、數量限制與 terminal state 是否等價。
4. skill ID、路徑、命令、程式碼與控制標籤是否保持原樣。
5. 中文是否自然易讀，但沒有順手重設計上游方法論。
6. 特別檢查上游刻意使用的 `human partner`、Red Flags 與 rationalization wording 是否因翻譯產生行為偏差。

### Deliverable

- 在 PR #2 留下一份分類 review。
- Blocker／major findings 需指出對應原文 invariant。
- 本輪不要直接修改檔案。
- 若沒有 blocker，也要明確列出已檢查項目與剩餘風險。

## Boundaries

- 不翻譯下一批 skill。
- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不 merge PR #2。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- 不直接修改檔案；本輪只留下 review findings。

## Resume Target

Claude review 完成後：

1. 主 agent 彙整 findings。
2. 使用者決定哪些 findings 可進入 `patch-approved`。
3. 修正並執行至少一輪中文壓力案例與英文回歸案例。
4. 交接機制驗證後合併 PR #2。
5. 第一批驗證通過後，再從最新 `main` 建立新分支，處理 `writing-plans` 與 `using-git-worktrees`。

## Last Handoff

- Created by: ChatGPT / OpenAI lineage
- Date: 2026-07-17
- Next agent: Claude／Anthropic lineage
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: PR #2
