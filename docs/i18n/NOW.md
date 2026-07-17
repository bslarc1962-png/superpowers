# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1–3 已全部合併。Phase 4（meta 與大型 reference）進行中:`writing-skills/SKILL.md` 本體已合併(PR #12);本批為 `writing-skills` 的 3 個核心 reference（testing-skills-with-subagents、persuasion-principles、examples/CLAUDE_MD_TESTING）已於目前分支完成初譯,尚待獨立語意 review 與合併。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開,HEAD＝da4df16）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1–#3:基礎文件、handoff、Phase 1 全部。
  - PR #4–#7:Phase 2 全部。
  - PR #8–#11:Phase 3 全部（requesting-code-review、receiving-code-review、dispatching-parallel-agents、systematic-debugging＋3 refs）。
  - PR #12:Phase 4 `writing-skills/SKILL.md` 本體（reviewer 0/0/0/1-note）。

## Current Scope

已進入 `main`:Phase 1–3 全部 skill、`writing-skills/SKILL.md`、`docs/i18n/` 全部文件。

目前分支尚未合併（Phase 4,writing-skills references）:

- `skills/writing-skills/testing-skills-with-subagents.md`（初譯,384 行）
- `skills/writing-skills/persuasion-principles.md`（初譯,187 行）
- `skills/writing-skills/examples/CLAUDE_MD_TESTING.md`（初譯,189 行）
- `docs/i18n/behavior-test-cases.md`（新增 WSR-01–05 情境測試規格）

**刻意不譯（使用者已裁示）**:

- `skills/writing-skills/anthropic-best-practices.md`（1150 行）——Anthropic 官方 skill 指引的原封 vendored 副本,非本專案行為核心(專案 CLAUDE.md 明言其 skill 哲學刻意不同於 Anthropic 官方),投報比低。使用者裁示:「如果對我真的沒有幫助就不用譯」→ **跳過,不在地化**。
- `skills/writing-skills/graphviz-conventions.dot`、`render-graphs.js`——程式碼／腳本。

## 本批翻譯不變式（自查已過）

- 3 檔的「所有 fenced 區塊」逐字保留英文,經 awk 抽取＋diff 確認與 `1ef568f` **byte-identical**:
  - testing-skills:壓力情境範例、`<Before>/<After>` specimen、合理化表範例、Red Flag 範例、`description` yaml、後設測試區塊(含 `your human partner:`)。
  - persuasion:7 組 ✅／❌ markdown specimen(示範 skill 撰寫語氣,如 `YOU MUST`／`No exceptions`)。
  - CLAUDE_MD_TESTING:4 個測試情境 ＋ 5 個文件變體(NULL/A/B/C/D)——這些是 A/B 測試的「受測資料」,翻譯會破壞實驗,必須逐字。
- mandatory anchor:必要背景（REQUIRED BACKGROUND）、必須（MUST）、不要用於強制遵循（DON'T USE）。
- RED-GREEN-REFACTOR／RED／GREEN／REFACTOR／VERIFY phase 標籤保留(5× RGR);7 大原則名以「中文（English）」呈現,表格內保留英文原名。
- 表格:TDD 對應 6／壓力類型 7／Quick Ref 6（testing）、原則組合 4（persuasion）data 列與對應皆等價;19 個 checkbox 保留;citation（Cialdini 2021、Meincke et al. 2025）逐字。
- refs（`persuasion-principles.md`、`examples/CLAUDE_MD_TESTING.md`、`superpowers:test-driven-development`）逐字;`2025-10-03`、`N=28,000`、百分比保留。
- 「your human partner」:testing 散文 1 處→「你合作的人類使用者」;fence 內(CLAUDE_MD 情境 4 處、testing 後設測試 1 處)保留英文。

## 本批獨立 review 結果

未參與初譯的 reviewer 逐位 diff 全部 fenced 區塊＋逐段對照:**0 blocker、0 major、0 minor、1 note**,無需修改。fenced 區塊 byte-identical(testing 14/14、persuasion 5/5、CLAUDE_MD 8/8);「your human partner」拆分正確(散文 1 處中文、fence 內全英文);mandatory anchor、RED-GREEN-REFACTOR(5×)、4 個表格與對應、19 checkbox、Cialdini/Meincke citation(含 `N=28,000`／`33% → 72%`／`p < .001`)、cross-ref、section 結構(30/30、15/15、19/19)皆等價。

- Note（入 Backlog）:半形逗號,typography,無行為影響。

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 `writing-skills` 的 3 個核心 reference;補情境測試規格;anthropic-best-practices／程式碼刻意不譯。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- 所有 fenced 區塊(specimen、測試情境、受測文件變體、程式碼、yaml)逐字不可改。
- `anthropic-best-practices.md`、`.dot`、`.js` 不在本批範圍。
- description／說服原則等內容屬中立在地化,非改動 skill philosophy。

## Resume Target

1. 獨立語意 review（未參與初譯的 reviewer 做中英逐段對照,含 3 檔 fenced 區塊逐位 diff）。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 WSR-01–05 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/` 未 clone,需 tmux 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. 本批合併後,`writing-skills` 除 anthropic-best-practices（刻意跳過）與程式碼外全部完成。**核心 skill 全數在地化完畢**。剩餘:平台 reference、README／install 等安裝文件;以及是否處理 `CREATION-LOG.md`、eval fixtures。最後做 Backlog typography 收尾批次。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`、`harness`、`checkpoint` 等在多個 skill 以裸英文出現,未於各檔首次出現處補中文 gloss。建議累積後一次統一整理。
- 全形／半形逗號跨檔不一致:PR #1 用全形「，」;PR #3 起多用半形「,」。屬排版一致性,無行為影響。列為專門 typography 批次（只改散文、跳過 ``` 區塊）。
- description 分隔符不一致:多數 skill 用「`. `」,但 `subagent-driven-development`（PR #7）用全形「。」無空格。留待 typography 掃描一併正規化。
- `anthropic-best-practices.md`（Phase 4 大型 reference）刻意未在地化——若日後決定要譯,需另開批次(1150 行)。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 4 `writing-skills` 的 3 個核心 reference,並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
