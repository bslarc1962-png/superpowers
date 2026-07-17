# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1 的四個 skill 已全部完成初譯。前兩個（`using-superpowers`、`brainstorming`）已在 `main`；後兩個（`writing-plans`、`using-git-worktrees`）已於目前分支完成初譯，尚待驗證與合併。同時已套用 PR #2 review 的交接類與 B3 findings。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`
- Base: `main`
- PR: 尚未開啟（等待使用者決定是否開 PR）
- Previous delivery:
  - PR #1 已合併：本地化基礎文件 ＋ `using-superpowers`、`brainstorming`
  - PR #2 已合併（2026-07-16）：durable handoff 文件（`agent-handoff.md`、`NOW.md`、`README.md`）。review findings 留在 PR #2 conversation。

## Current Scope

已進入 `main`：

- `docs/i18n/translation-guide.md`
- `docs/i18n/glossary.md`
- `docs/i18n/roadmap.md`
- `docs/i18n/behavior-test-cases.md`
- `docs/i18n/README.md`
- `docs/i18n/agent-handoff.md`
- `docs/i18n/NOW.md`
- `skills/using-superpowers/SKILL.md`
- `skills/brainstorming/SKILL.md`

目前分支尚未合併：

- `skills/writing-plans/SKILL.md`（初譯）
- `skills/writing-plans/plan-document-reviewer-prompt.md`（初譯；派給 subagent 的 dispatch prompt 刻意保留英文）
- `skills/using-git-worktrees/SKILL.md`（初譯）
- `skills/using-superpowers/SKILL.md`（套用 B3：`human partner` → 「合作的人類使用者」）
- `docs/i18n/agent-handoff.md`（套用 A2：範例改為 PR-agnostic；A4：補結尾換行）
- `docs/i18n/NOW.md`（套用 A1／A3：對齊實況與日期）

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate` ＋ `patch-approved`（本輪已由使用者授權）

### Assignment

1. 初譯 `writing-plans` 與 `using-git-worktrees`（Phase 1 剩餘兩個 skill）。
2. 套用 PR #2 review 中使用者批准的 findings：交接類（A1、A2、A3、A4）與 B3（`human partner`）。

本輪未修改 B1／B2／B4——review 已判定其為 fork 既定方法論的刻意選擇，非翻譯疏漏。

## Boundaries

- 不翻譯 Phase 2 之後的 skill。
- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- 未取得使用者授權前不 merge、不開 PR。

## Resume Target

本批初譯完成後：

1. 由未參與本輪初譯的 reviewer 對 `writing-plans` 與 `using-git-worktrees` 做獨立語意 review（gate、順序、終止狀態、強制詞、識別字）。
2. 執行至少一輪中文壓力案例與英文回歸案例（Phase 1 尚未完成實際 harness 行為 eval，PR #1 亦標記此項未完成）。
3. 使用者決定是否為本批開 PR 並合併。
4. 驗證通過後，從最新 `main` 建立新分支，進入 Phase 2（`test-driven-development`、`executing-plans`、`subagent-driven-development`、`verification-before-completion`、`finishing-a-development-branch`）。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 1 剩餘 skill ＋ 套用 PR #2 review findings
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
