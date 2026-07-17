# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1 已全部合併。Phase 2（實作與驗證主迴圈）本批為**最後一個 skill**:`subagent-driven-development`(＋ `implementer-prompt.md`、`task-reviewer-prompt.md`)已於目前分支完成初譯,尚待獨立語意 review 與合併。合併後 Phase 2 全數完成。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開,HEAD＝ae05a08）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1 已合併:本地化基礎文件 ＋ `using-superpowers`、`brainstorming`
  - PR #2 已合併:durable handoff 文件。review findings 留在 PR #2 conversation。
  - PR #3 已合併:`writing-plans`、`using-git-worktrees`（Phase 1 完成）。
  - PR #4 已合併:`executing-plans`、`verification-before-completion`（Phase 2 第一批）。
  - PR #5 已合併:`finishing-a-development-branch`（Phase 2 第二批,reviewer 0/0/0/2-note）。
  - PR #6 已合併:`test-driven-development`、`testing-anti-patterns.md`（Phase 2 第三批,reviewer 0/0/1/3-note）。

## Current Scope

已進入 `main`:

- `docs/i18n/`:`translation-guide.md`、`glossary.md`、`roadmap.md`、`behavior-test-cases.md`、`README.md`、`agent-handoff.md`、`NOW.md`
- Phase 1 skill:`using-superpowers`、`brainstorming`、`writing-plans`（＋ `plan-document-reviewer-prompt.md`）、`using-git-worktrees`
- Phase 2 skill:`executing-plans`、`verification-before-completion`、`finishing-a-development-branch`、`test-driven-development`（＋ `testing-anti-patterns.md`）

目前分支尚未合併（Phase 2 第四批,最後一批）:

- `skills/subagent-driven-development/SKILL.md`（初譯）
- `skills/subagent-driven-development/implementer-prompt.md`（散文初譯;``` 派遣 payload 保留英文）
- `skills/subagent-driven-development/task-reviewer-prompt.md`（散文初譯;``` 派遣 payload 保留英文）
- `docs/i18n/behavior-test-cases.md`（新增 `subagent-driven-development` SDD-01–07 情境測試規格）
- **未翻譯（刻意）**:`skills/subagent-driven-development/scripts/`（review-package、sdd-workspace、task-brief 皆為程式碼）

## 本批翻譯不變式（自查已過）

- description 加中英雙段（原文僅英文 trigger）。
- 兩個 Graphviz `dot` 圖（when_to_use、process）整塊保留英文,節點 ID 與 label 未動。
- 狀態 token 逐字保留:`DONE`／`DONE_WITH_CONCERNS`／`NEEDS_CONTEXT`／`BLOCKED`。
- 腳本路徑、指令逐字保留:`scripts/review-package`、`scripts/task-brief`、`HEAD~1`、`git merge-base main HEAD`、`.superpowers/sdd/progress.md`、ledger 樣板 `Task N: complete (commits <base7>..<head7>, review clean)`。
- skill ID（`superpowers:*`）與檔案連結逐字保留。
- 兩個 prompt reference 的 ``` fenced 區塊＝實際送給 subagent 的 payload,**保留英文原樣**;只翻譯區塊外散文與 Placeholders 清單。`[MODEL]`／`[BRIEF_FILE]`… 等佔位符逐字保留。
- 「human partner」→「你合作的人類使用者」;mandatory-word 錨定 必須（MUST）／絕對不要（Never）。

## 本批獨立 review 結果

未參與初譯的 reviewer 對三個檔案做中英逐段對照:**0 blocker、0 major、0 minor、2 note**。高風險項全數等價——兩個 prompt 的 ``` 派遣 payload 經逐行 diff 與 `ae05a08` **完全一致**;mandatory-word 錨定、兩個 Graphviz 圖、狀態 token、腳本路徑、ledger 樣板、skill ID、placeholder、清單數量與順序、「你合作的人類使用者」用詞皆保留。

- Note 1（已套用）:H1 原作「子代理驅動開發（Subagent-Driven Development）」中的 `subagent` 依 glossary 應保留英文 → 改為 `# Subagent-Driven Development（子代理驅動開發）`。
- Note 2（保留,不動作）:兩個 prompt 檔在區塊外新增一句護欄性譯註（「以下 ``` 區塊…保留英文原樣」）為原文所無,但位於 fenced 區塊外、且主動保護「payload 不譯」不變式,reviewer 確認良性、無需修改。

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 `subagent-driven-development` 與其兩個 prompt reference,補對應情境測試規格;scripts/ 不譯。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- 兩個 Graphviz 圖、所有 fenced 派遣 payload、狀態 token、腳本／指令／路徑、skill ID 皆不可改。
- `scripts/` 目錄為程式碼,不翻譯。
- description 的中英段刻意不逐字對稱為合規（translation-guide §2）。

## Resume Target

1. 獨立語意 review（未參與初譯的 reviewer 做中英逐段對照）。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 SDD-01–07 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/`(superpowers-evals)未 clone,需 tmux Claude Code／Codex 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. 本批合併後 **Phase 2 全數完成**。接著進入 Phase 3(除錯與協作:`systematic-debugging`、`dispatching-parallel-agents`、`requesting-code-review`、`receiving-code-review`)與 Phase 4(meta 與大型 reference:`writing-skills`、平台 reference、README／install)。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`(基準)、`harness`(agent 執行框架)、`checkpoint`(檢查點)在多個 skill 以裸英文出現,未於各檔首次出現處補中文 gloss。建議累積數個 skill 後一次統一整理,避免逐檔零星 diff。
- 全形／半形逗號跨檔不一致:PR #1 的 `using-superpowers`／`brainstorming` 用全形「，」;PR #3 起本 lineage 的翻譯多用半形「,」。屬排版一致性,無行為影響。需一次跨檔統一(建議全形，對齊 repo 既有 zh 文件),但 sed 會誤改程式碼區塊內的逗號,須以只改散文、跳過 ``` 區塊的方式處理,列為專門 patch 批次。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 2 第四批（最後一批,`subagent-driven-development` ＋ 兩個 prompt reference）並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
