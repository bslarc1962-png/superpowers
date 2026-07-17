# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1 已全部合併。Phase 2（實作與驗證主迴圈）進行中:`executing-plans`、`verification-before-completion`、`finishing-a-development-branch` 已合併(PR #4、#5);本批 `test-driven-development`(＋ `testing-anti-patterns.md`)已於目前分支完成初譯,尚待獨立語意 review、harness 行為 eval 與合併。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1 已合併:本地化基礎文件 ＋ `using-superpowers`、`brainstorming`
  - PR #2 已合併:durable handoff 文件。review findings 留在 PR #2 conversation。
  - PR #3 已合併:`writing-plans`、`using-git-worktrees`（Phase 1 完成）。
  - PR #4 已合併:`executing-plans`、`verification-before-completion`（Phase 2 第一批）。
  - PR #5 已合併:`finishing-a-development-branch`（Phase 2 第二批,reviewer 0/0/0/2-note）。

## Current Scope

已進入 `main`:

- `docs/i18n/`:`translation-guide.md`、`glossary.md`、`roadmap.md`、`behavior-test-cases.md`、`README.md`、`agent-handoff.md`、`NOW.md`
- Phase 1 skill:`using-superpowers`、`brainstorming`、`writing-plans`（＋ `plan-document-reviewer-prompt.md`）、`using-git-worktrees`
- Phase 2 skill:`executing-plans`、`verification-before-completion`、`finishing-a-development-branch`

目前分支尚未合併（Phase 2 第三批）:

- `skills/test-driven-development/SKILL.md`（初譯）
- `skills/test-driven-development/testing-anti-patterns.md`（初譯）
- `docs/i18n/behavior-test-cases.md`（新增 `test-driven-development` TDD-01–07 情境測試規格）

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 `test-driven-development` 與其 reference `testing-anti-patterns.md`,並補對應情境測試規格。

## Boundaries

- 不翻譯本批以外的 Phase 2 skill（`subagent-driven-development` 留給下一批）。
- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- `RED-GREEN-REFACTOR`、Graphviz 流程圖(節點 ID 與 label)、所有程式碼區塊、Iron Law 英文原行皆不可改。
- description 的中英段刻意不逐字對稱為合規（translation-guide §2）,勿「補齊」中文 description。

## Resume Target

1. 由未參與本輪初譯的 reviewer 對 `test-driven-development` 與 `testing-anti-patterns.md` 做獨立語意 review(鐵律、RED-GREEN-REFACTOR 順序、驗證 RED／GREEN 強制性、合理化表 11 列、Red Flags 13 項、反模式 5 項與其 Gate Function、程式碼區塊原樣)。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 TDD-01–07 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/`(superpowers-evals)未 clone,需 tmux Claude Code／Codex 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. Phase 2 最後一批:`subagent-driven-development`(＋ implementer/task-reviewer prompt 與 scripts)。之後進入 Phase 3(除錯與協作)與 Phase 4(meta 與大型 reference)。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`(基準)、`harness`(agent 執行框架)、`checkpoint`(檢查點)在多個 skill 以裸英文出現,未於各檔首次出現處補中文 gloss。建議累積數個 skill 後一次統一整理,避免逐檔零星 diff。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 2 第三批(`test-driven-development` ＋ `testing-anti-patterns.md`)並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
