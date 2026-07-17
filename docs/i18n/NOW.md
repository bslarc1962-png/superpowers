# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1 已全部合併。Phase 2（實作與驗證主迴圈）進行中:`executing-plans`、`verification-before-completion` 已合併(PR #4);本批 `finishing-a-development-branch` 已於目前分支完成初譯,尚待獨立語意 review、harness 行為 eval 與合併。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1 已合併:本地化基礎文件 ＋ `using-superpowers`、`brainstorming`
  - PR #2 已合併:durable handoff 文件。review findings 留在 PR #2 conversation。
  - PR #3 已合併:`writing-plans`、`using-git-worktrees`（Phase 1 完成）。
  - PR #4 已合併:`executing-plans`、`verification-before-completion`（Phase 2 第一批）。獨立 reviewer 判定 0 blocker／0 major,findings 已套用。

## Current Scope

已進入 `main`:

- `docs/i18n/`:`translation-guide.md`、`glossary.md`、`roadmap.md`、`behavior-test-cases.md`、`README.md`、`agent-handoff.md`、`NOW.md`
- Phase 1 skill:`using-superpowers`、`brainstorming`、`writing-plans`（＋ `plan-document-reviewer-prompt.md`）、`using-git-worktrees`
- Phase 2 skill:`executing-plans`、`verification-before-completion`

目前分支尚未合併（Phase 2 第二批）:

- `skills/finishing-a-development-branch/SKILL.md`（初譯）
- `docs/i18n/behavior-test-cases.md`（新增 `finishing-a-development-branch` FDB-01–07 情境測試規格）

## 本批獨立 review 結果

由未參與初譯的 reviewer 對 `finishing-a-development-branch` 做中英逐段對照:**0 blocker、0 major、0 minor、2 note**,行為層面完全等價,**本批無需修改**。已驗證:4／3 選項選單完整、環境偵測表 3 列、清理 provenance(只在選項 1／4、只清 `.worktrees/`／`worktrees/` 自建、harness 擁有不移除、先 merge 再移 worktree 再刪 branch、移除前 cd 主 repo、移除後 prune)、`discard` literal token 保留、快速參考 4 列 yes/- 對映、常見錯誤 7、Red Flags Never 7／Always 7、所有 git 命令與 placeholder 原樣。

- 兩個 note 皆為 casing 風格觀察(小寫 `only`→「只」而非「只能（ONLY）」;`Never`／`Always` 保留原文 title-case)。與本專案既定政策一致(小寫 inline 詞不加大寫英文錨點),reviewer 判定無行為影響、不需修改。

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 `finishing-a-development-branch`,並補對應情境測試規格。

## Boundaries

- 不翻譯本批以外的 Phase 2 skill（`test-driven-development`、`subagent-driven-development` 留給後續批次）。
- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- description 的中英段刻意不逐字對稱為合規（translation-guide §2）,勿「補齊」中文 description。

## Resume Target

1. ~~獨立語意 review~~（已完成,0 blocker／0 major／0 minor,本批無需修改）。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 FDB-01–07 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/`(superpowers-evals)未 clone,需 tmux Claude Code／Codex 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. Phase 2 剩餘批次:`test-driven-development`(＋ `testing-anti-patterns.md` 大型 reference)、`subagent-driven-development`(＋ implementer/task-reviewer prompt 與 scripts)。之後進入 Phase 3(除錯與協作)與 Phase 4(meta 與大型 reference)。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`(基準)、`harness`(agent 執行框架)、`checkpoint`(檢查點)在多個 skill 以裸英文出現,未於各檔首次出現處補中文 gloss。建議累積數個 skill 後一次統一整理,避免逐檔零星 diff。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 2 第二批(`finishing-a-development-branch`)並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
