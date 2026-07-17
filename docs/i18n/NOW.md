# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1、Phase 2 已全部合併。Phase 3（除錯與協作）本批為**最後一個 skill**:`systematic-debugging`（＋ 3 個技巧 reference）已於目前分支完成初譯,尚待獨立語意 review 與合併。合併後 Phase 3 全數完成,只剩 Phase 4（meta 與大型 reference）。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開,HEAD＝4a8855d）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1–#3 已合併:基礎文件、handoff、Phase 1 全部。
  - PR #4–#7 已合併:Phase 2 全部。
  - PR #8 已合併:`requesting-code-review`（＋ `code-reviewer.md`）。
  - PR #9 已合併:`receiving-code-review`（＋ translation-guide §6 雙語 blocklist 規則）。
  - PR #10 已合併:`dispatching-parallel-agents`,reviewer 0/0/1-minor/3-note。

## Current Scope

已進入 `main`:

- `docs/i18n/` 全部文件
- Phase 1、Phase 2 全部 skill
- Phase 3 skill:`requesting-code-review`（＋ `code-reviewer.md`）、`receiving-code-review`、`dispatching-parallel-agents`

目前分支尚未合併（Phase 3 第四批,最後一批）:

- `skills/systematic-debugging/SKILL.md`（初譯,296 行）
- `skills/systematic-debugging/root-cause-tracing.md`（初譯）
- `skills/systematic-debugging/defense-in-depth.md`（初譯）
- `skills/systematic-debugging/condition-based-waiting.md`（初譯）
- `docs/i18n/behavior-test-cases.md`（新增 SD-01–07 情境測試規格）

**未翻譯（刻意,已判定範圍）**:

- `test-academic.md`、`test-pressure-1/2/3.md` — eval harness 的測試 fixture（考核／壓力情境輸入,非 skill 行為本身),屬 eval 材料不在本地化範圍。
- `CREATION-LOG.md` — 歷史／meta 開發紀錄（Phase 4 才考慮）。
- `condition-based-waiting-example.ts`、`find-polluter.sh` — 程式碼／腳本,不翻譯。

## 本批翻譯不變式（自查已過）

- description 加中英雙段,分隔用「`. `」。
- 鐵律雙語框:「沒有先做根本原因調查，就不得進行任何修正 / NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST」（英文行逐字保留）。
- 四個 Phase 標題與順序、Phase 4.5「3+ 修正質疑架構」、`≥ 3`／`< 3` 邏輯保留。
- 3 個 Graphviz `dot` 圖（rct 2、cbw 1）整塊保留英文,含紅色 octagon「NEVER fix just the symptom」。
- 所有 bash／typescript 程式碼區塊逐字保留（含註解、`execFileAsync`、`waitFor` 簽章、`NODE_ENV` guard、`1847 tests`、instrumentation）。SKILL 內的多元件 bash 蒐證區塊與縮排的 pseudocode 區塊保留。
- mandatory anchor:一律（ALWAYS）、必須（MUST）、尤其（ESPECIALLY）、一次一個（ONE）、完整（COMPLETELY）、絕對不要（NEVER,rct）。
- 合理化表 8 列、Quick Reference 4 列保留;reference 檔名（`root-cause-tracing.md` 等）、`find-polluter.sh`、`condition-based-waiting-example.ts`、skill ID 逐字保留。
- 「your human partner」→「你合作的人類使用者」(2 處:Phase 4.5、訊號段標題)。Red Flags 內心獨白與人類訊號 cue 譯為中文（人類訊號附原英文於 Ultra-think 一項）。

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 `systematic-debugging` 與其 3 個技巧 reference;補情境測試規格;測試 fixture／CREATION-LOG／程式碼刻意不譯。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- 3 個 dot 圖、所有程式碼區塊、鐵律英文行、Phase 結構與計數邏輯皆不可改。
- test-*.md、CREATION-LOG.md、.ts、.sh 不在本批範圍。
- description 中英段刻意不逐字對稱為合規（translation-guide §2）。

## Resume Target

1. 獨立語意 review（未參與初譯的 reviewer 做中英逐段對照,含 3 個 reference 的程式碼區塊逐字性）。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 SD-01–07 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/`(superpowers-evals)未 clone,需 tmux Claude Code／Codex 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. 本批合併後 **Phase 3 全數完成**。接著 Phase 4:`writing-skills`（meta,最大）、平台 reference、README／install;以及是否要處理 `CREATION-LOG.md`、eval fixtures 的在地化。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`、`harness`、`checkpoint` 等在多個 skill 以裸英文出現,未於各檔首次出現處補中文 gloss。建議累積後一次統一整理。
- 全形／半形逗號跨檔不一致:PR #1 用全形「，」;PR #3 起多用半形「,」。屬排版一致性,無行為影響。列為專門 typography 批次（只改散文、跳過 ``` 區塊）。
- description 分隔符不一致:多數 skill 用「`. `」,但 `subagent-driven-development`（PR #7）用全形「。」無空格。留待與逗號一起做 typography 掃描時一併正規化。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 3 第四批（最後一批,`systematic-debugging` ＋ 3 個技巧 reference）並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
