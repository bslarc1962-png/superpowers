# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1、Phase 2 已全部合併。Phase 3（除錯與協作）開始:本批 `requesting-code-review`（＋ `code-reviewer.md`）已於目前分支完成初譯,尚待獨立語意 review 與合併。選它作為 Phase 3 起點,是因為 `subagent-driven-development` 的最終整個 branch review 引用它的 `code-reviewer.md`,補上後整條引用鏈的中文覆蓋才完整。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開,HEAD＝56cb93a）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1 已合併:本地化基礎文件 ＋ `using-superpowers`、`brainstorming`
  - PR #2 已合併:durable handoff 文件。
  - PR #3 已合併:`writing-plans`、`using-git-worktrees`（Phase 1 完成）。
  - PR #4 已合併:`executing-plans`、`verification-before-completion`。
  - PR #5 已合併:`finishing-a-development-branch`。
  - PR #6 已合併:`test-driven-development`、`testing-anti-patterns.md`。
  - PR #7 已合併:`subagent-driven-development`（＋ 兩個 prompt reference）——Phase 2 完成。

## Current Scope

已進入 `main`:

- `docs/i18n/`:`translation-guide.md`、`glossary.md`、`roadmap.md`、`behavior-test-cases.md`、`README.md`、`agent-handoff.md`、`NOW.md`
- Phase 1 skill:`using-superpowers`、`brainstorming`、`writing-plans`（＋ `plan-document-reviewer-prompt.md`）、`using-git-worktrees`
- Phase 2 skill:`executing-plans`、`verification-before-completion`、`finishing-a-development-branch`、`test-driven-development`（＋ `testing-anti-patterns.md`）、`subagent-driven-development`（＋ `implementer-prompt.md`、`task-reviewer-prompt.md`）

目前分支尚未合併（Phase 3 第一批）:

- `skills/requesting-code-review/SKILL.md`（初譯）
- `skills/requesting-code-review/code-reviewer.md`（散文初譯;兩個 ``` 區塊——派遣 payload 與範例輸出——保留英文原樣。git diff 已確認只動到區塊外的散文行）
- `docs/i18n/behavior-test-cases.md`（新增 RCR-01–06 情境測試規格）

## 本批翻譯不變式（自查已過）

- description 加中英雙段（原文僅英文 trigger）。
- `code-reviewer.md` 兩個 ``` fenced 區塊(Senior Code Reviewer 派遣 payload、Example Output 範例)保留英文;只翻譯區塊外散文、Placeholders 清單與「Reviewer 回傳」行。`[DESCRIPTION]`／`[BASE_SHA]`… 佔位符與 SKILL 的 `{DESCRIPTION}`… 皆逐字保留。
- bash SHA 指令(`git rev-parse HEAD~1`、`git rev-parse HEAD`)、範例中的 SHA 值與 DESCRIPTION／PLAN_OR_REQUIREMENTS 欄位值逐字保留;範例中 reviewer 輸出標籤(Strengths／Issues／Assessment)保留英文,因 payload 本身即輸出英文。
- mandatory-word 錨定:絕對不要（Never）。skill 連結 `code-reviewer.md`、severity 標籤 Critical／Important／Minor 保留。

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 `requesting-code-review` 與其 reference `code-reviewer.md`,補對應情境測試規格。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- `code-reviewer.md` 的兩個 fenced 區塊、severity 標籤、SHA 指令與佔位符皆不可改。
- description 的中英段刻意不逐字對稱為合規（translation-guide §2）。

## Resume Target

1. 獨立語意 review（未參與初譯的 reviewer 做中英逐段對照）。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 RCR-01–06 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/`(superpowers-evals)未 clone,需 tmux Claude Code／Codex 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. Phase 3 其餘:`systematic-debugging`、`dispatching-parallel-agents`、`receiving-code-review`。之後 Phase 4(meta 與大型 reference:`writing-skills`、平台 reference、README／install)。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`(基準)、`harness`(agent 執行框架)、`checkpoint`(檢查點)在多個 skill 以裸英文出現,未於各檔首次出現處補中文 gloss。建議累積數個 skill 後一次統一整理,避免逐檔零星 diff。
- 全形／半形逗號跨檔不一致:PR #1 的 `using-superpowers`／`brainstorming` 用全形「，」;PR #3 起本 lineage 的翻譯多用半形「,」。屬排版一致性,無行為影響。須以只改散文、跳過 ``` 區塊的方式處理,列為專門 patch 批次。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 3 第一批（`requesting-code-review` ＋ `code-reviewer.md`）並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
