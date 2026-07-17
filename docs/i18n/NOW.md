# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1、Phase 2 已全部合併。Phase 3（除錯與協作）進行中:`requesting-code-review`（PR #8）、`receiving-code-review`（PR #9）已合併;本批 `dispatching-parallel-agents` 已於目前分支完成初譯,尚待獨立語意 review 與合併。合併後 Phase 3 只剩 `systematic-debugging`。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開,HEAD＝1feebb4）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1–#3 已合併:基礎文件、handoff、Phase 1 全部。
  - PR #4–#7 已合併:Phase 2 全部。
  - PR #8 已合併:Phase 3 `requesting-code-review`（＋ `code-reviewer.md`）。
  - PR #9 已合併:Phase 3 `receiving-code-review`（＋ translation-guide §6 雙語 blocklist 規則）,reviewer 0/0/2-minor/1-note,兩 minor 已套用。

## Current Scope

已進入 `main`:

- `docs/i18n/`:`translation-guide.md`（§6 含雙語 blocklist 規則）、`glossary.md`、`roadmap.md`、`behavior-test-cases.md`、`README.md`、`agent-handoff.md`、`NOW.md`
- Phase 1、Phase 2 全部 skill
- Phase 3 skill:`requesting-code-review`（＋ `code-reviewer.md`）、`receiving-code-review`

目前分支尚未合併（Phase 3 第三批）:

- `skills/dispatching-parallel-agents/SKILL.md`（初譯,單檔）
- `docs/i18n/behavior-test-cases.md`（新增 DPA-01–06 情境測試規格）

## 本批翻譯不變式（自查已過）

- description 加中英雙段,分隔用「`. `」。
- 一個 Graphviz `dot` 圖（when_to_use）整塊保留英文。
- **4 個 ``` 區塊全部保留英文**:dot 圖、`text` 派遣範例（Subagent 呼叫）、`markdown` agent-prompt 範例、Real Example 的 Dispatch 對照。理由:皆為 agent 派遣 payload／技術範例。區塊外散文全數翻譯。
- 測試檔名逐字保留:`agent-tool-abort.test.ts`、`batch-completion-behavior.test.ts`、`tool-approval-race-conditions.test.ts`;技術 token（timing、race condition、threadId、execution count、timeout）保留。
- mandatory anchor:「絕對不應繼承」（never inherit）、Common Mistakes ✅ 約束處「不要（Do NOT）改動正式程式碼」。8 個 ✅／❌ 標記保留。
- 本 skill 無「your human partner」用語。Overview 首段與 `subagent-driven-development` 的委派段用詞一致（術語承接）。

## 本批獨立 review 結果

未參與初譯的 reviewer 中英逐段對照:**0 blocker、0 major、1 minor、3 note**。reviewer 獨立抽取 4 個 fence 與 `1feebb4` diff,確認全部 byte-identical;14 個標題、Pattern 4 步、Common Mistakes 8 標記、測試檔名與技術 token 數量皆等價。

- Minor（已套用）:line 138 `refactoring` 依 glossary 應為「重構（refactor）」含英文 anchor（首次出現）→ 改「大型重構（refactoring）後」。
- Note 1（已套用）:`Do NOT` glossary 標準為「不得（Do NOT）」（比「不要」更強的禁止）→ line 124 改「不得（Do NOT）改動正式程式碼」。
- Note 2（已套用）:`event-based waiting` fence 內已逐字保留,Results 散文改為 token 並列「event-based waiting（以事件為基礎的等待）」以求跨區塊一致。
- Note 3（無行為影響,入 Backlog）:5 個半形逗號,留待跨檔 typography 掃描。

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 `dispatching-parallel-agents`（單檔）並補情境測試規格。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- Graphviz 圖、4 個 fenced 區塊、測試檔名與技術 token 皆不可改。
- description 中英段刻意不逐字對稱為合規（translation-guide §2）。

## Resume Target

1. 獨立語意 review（未參與初譯的 reviewer 做中英逐段對照）。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 DPA-01–06 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/`(superpowers-evals)未 clone,需 tmux Claude Code／Codex 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. Phase 3 最後一批:`systematic-debugging`（Phase 3 最大,可能含多檔 reference）。之後 Phase 4(meta 與大型 reference:`writing-skills`、平台 reference、README／install)。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`(基準)、`harness`(agent 執行框架)、`checkpoint`(檢查點)在多個 skill 以裸英文出現,未於各檔首次出現處補中文 gloss。建議累積數個 skill 後一次統一整理。
- 全形／半形逗號跨檔不一致:PR #1 用全形「，」;PR #3 起多用半形「,」。屬排版一致性,無行為影響。須以只改散文、跳過 ``` 區塊的方式處理,列為專門 typography 批次。
- description 分隔符不一致:多數 skill 用「`. `」,但 `subagent-driven-development`（PR #7）用全形「。」無空格。留待與逗號一起做 typography 掃描時一併正規化。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 3 第三批（`dispatching-parallel-agents`）並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
