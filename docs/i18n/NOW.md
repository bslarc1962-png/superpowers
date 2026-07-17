# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1、Phase 2 已全部合併。Phase 3（除錯與協作）進行中:`requesting-code-review` 已合併(PR #8);本批 `receiving-code-review` 已於目前分支完成初譯,尚待獨立語意 review 與合併。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開,HEAD＝54b2944）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1–#3 已合併:基礎文件、handoff、Phase 1 全部(`using-superpowers`、`brainstorming`、`writing-plans`、`using-git-worktrees`)。
  - PR #4–#7 已合併:Phase 2 全部(`executing-plans`、`verification-before-completion`、`finishing-a-development-branch`、`test-driven-development`＋anti-patterns、`subagent-driven-development`＋兩個 prompt reference)。
  - PR #8 已合併:Phase 3 `requesting-code-review`（＋ `code-reviewer.md`）,reviewer 0/0/0/2-note。

## Current Scope

已進入 `main`:

- `docs/i18n/`:`translation-guide.md`（§6 新增「禁止語 specimen 保留雙語」規則）、`glossary.md`、`roadmap.md`、`behavior-test-cases.md`、`README.md`、`agent-handoff.md`、`NOW.md`
- Phase 1、Phase 2 全部 skill（見上）
- Phase 3 skill:`requesting-code-review`（＋ `code-reviewer.md`）

目前分支尚未合併（Phase 3 第二批）:

- `skills/receiving-code-review/SKILL.md`（初譯,單檔）
- `docs/i18n/translation-guide.md`（§6 新增雙語 blocklist 規則）
- `docs/i18n/behavior-test-cases.md`（新增 RCV-01–07 情境測試規格）

## 本批翻譯不變式（自查已過）

- description 加中英雙段,分隔用「`. `」（對齊多數 skill）。
- **禁止語 specimen 雙語**（本批關鍵決定,已寫入 translation-guide §6）:must-not-say 清單（`You're absolutely right!`、`Great point!`、`Thanks for…` 等表演性附和）保留英文原句「並」附中文對照——雙語運作的 agent 兩種語言都可能反射性說出,只留一種會漏。而「應該說」的 good response／範例對話只譯為中文。
- 回應模式 6 步保留英文步驟標籤 `READ／UNDERSTAND／VERIFY／EVALUATE／RESPOND／IMPLEMENT`（中文＋括號英文）。
- mandatory anchor:絕對不要（NEVER）。12 個 ``` 區塊、Common Mistakes 表 7 列、`gh api …/comments/{id}/replies` 指令、`grep`、`YAGNI`、`bundle ID`／`build target`／`10.15+`／`13+` 等技術 token 逐字保留。
- 「your human partner」→「你合作的人類使用者」(9 處);line 129 的「your partner」→「你的夥伴」。

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 `receiving-code-review`（單檔）並補情境測試規格;新增 translation-guide §6 雙語 blocklist 規則。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容。
- Common Mistakes 表列數、回應模式步驟數與順序、`gh api` 指令、技術 token 皆不可改。
- description 中英段刻意不逐字對稱為合規（translation-guide §2）。

## Resume Target

1. 獨立語意 review（未參與初譯的 reviewer 做中英逐段對照,特別檢查禁止語雙語決定是否恰當、無行為漂移）。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 RCV-01–07 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/`(superpowers-evals)未 clone,需 tmux Claude Code／Codex 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. Phase 3 其餘:`systematic-debugging`、`dispatching-parallel-agents`。之後 Phase 4(meta 與大型 reference:`writing-skills`、平台 reference、README／install)。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`(基準)、`harness`(agent 執行框架)、`checkpoint`(檢查點)在多個 skill 以裸英文出現,未於各檔首次出現處補中文 gloss。建議累積數個 skill 後一次統一整理。
- 全形／半形逗號跨檔不一致:PR #1 用全形「，」;PR #3 起多用半形「,」。屬排版一致性,無行為影響。須以只改散文、跳過 ``` 區塊的方式處理,列為專門 typography 批次。
- description 分隔符不一致:多數 skill 用「`. `」,但 `subagent-driven-development`（PR #7）用全形「。」無空格。留待與逗號一起做 typography 掃描時一併正規化。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 3 第二批（`receiving-code-review`）＋ translation-guide §6 雙語 blocklist 規則,並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
