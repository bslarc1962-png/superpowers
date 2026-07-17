# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1（入口與設計流程）四個 skill 已全部合併至 `main`。目前進入 Phase 2（實作與驗證主迴圈）第一批:`executing-plans` 與 `verification-before-completion` 已於目前分支完成初譯,尚待獨立語意 review、harness 行為 eval 與合併。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1 已合併:本地化基礎文件 ＋ `using-superpowers`、`brainstorming`
  - PR #2 已合併（2026-07-16）:durable handoff 文件。review findings 留在 PR #2 conversation。
  - PR #3 已合併:`writing-plans`、`using-git-worktrees` ＋ PR #2 review findings ＋ 本批情境測試規格。獨立 reviewer 判定 0 blocker／0 major,findings 已套用。

## Current Scope

已進入 `main`（Phase 1 完成）:

- `docs/i18n/`:`translation-guide.md`、`glossary.md`、`roadmap.md`、`behavior-test-cases.md`、`README.md`、`agent-handoff.md`、`NOW.md`
- `skills/using-superpowers/SKILL.md`
- `skills/brainstorming/SKILL.md`
- `skills/writing-plans/SKILL.md`（＋ `plan-document-reviewer-prompt.md`）
- `skills/using-git-worktrees/SKILL.md`

目前分支尚未合併（Phase 2 第一批）:

- `skills/executing-plans/SKILL.md`（初譯）
- `skills/verification-before-completion/SKILL.md`（初譯）
- `docs/i18n/behavior-test-cases.md`（新增 `executing-plans` EP-01–06 與 `verification-before-completion` VBC-01–06 情境測試規格）

## 本批獨立 review 結果

由未參與初譯的 reviewer 對兩個 skill 做中英逐段對照:**0 blocker、0 major、1 minor、2 note**,行為層面判定完全等價(鐵律、Gate Function 五步、常見失誤 7 列、防止合理化 8 列、Red Flags 8 項、關鍵模式 5 組、STOP 條件、終止步驟、強制詞錨點均等價)。

- Finding 1（minor,已套用）:`executing-plans` description 的裸 `checkpoint` → 補為「檢查點（checkpoint）」,符合 glossary。
- Finding 2（note,已套用）:`verification-before-completion` 鐵律中文行「新鮮的驗證證據」→ 改為「當下重新跑過的驗證證據」,提升可讀性。
- Finding 3（note,**勿動**）:reviewer 確認兩份 description 的中英段刻意不逐字對稱是合規的（translation-guide §2:description 只描述何時載入）。英文段完整保留以命中英文觸發詞,中文段只留觸發條件。**後續請勿「補齊」中文 description,否則反而違反 §2。**

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 Phase 2 第一批兩個 skill:`executing-plans`、`verification-before-completion`,並補對應情境測試規格。

## Boundaries

- 不翻譯本批以外的 Phase 2 skill（`test-driven-development`、`subagent-driven-development`、`finishing-a-development-branch` 留給後續批次）。
- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。

## Resume Target

1. ~~獨立語意 review~~（已完成,0 blocker／0 major,findings 已套用）。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 EP-01–06、VBC-01–06 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/`(superpowers-evals)未 clone,需 tmux Claude Code／Codex 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. 合併後續 Phase 2 批次:`test-driven-development`(＋ `testing-anti-patterns.md` 大型 reference)、`subagent-driven-development`(＋ prompt reference 與 scripts)、`finishing-a-development-branch`。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`(基準)、`harness`(agent 執行框架)、`checkpoint`(檢查點)在多個 skill 以裸英文出現,未於各檔首次出現處補中文 gloss。建議累積數個 skill 後一次統一整理,避免逐檔零星 diff。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 2 第一批(`executing-plans`、`verification-before-completion`)並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
