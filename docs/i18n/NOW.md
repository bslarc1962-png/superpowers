# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

Phase 1–3 已全部合併。Phase 4（meta 與大型 reference）開始:`writing-skills` 是主體,但**很大**,拆成多個子批。**本批＝ writing-skills 的 SKILL.md（689 行）本體**,已於目前分支完成初譯,尚待獨立語意 review 與合併。其 4 個大型 reference 留給後續子批。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（前一批 PR 合併後,已從最新 `main` 重開,HEAD＝1ef568f）
- Base: `main`
- PR: 尚未開啟
- Previous delivery:
  - PR #1–#3 已合併:基礎文件、handoff、Phase 1 全部。
  - PR #4–#7 已合併:Phase 2 全部。
  - PR #8–#11 已合併:Phase 3 全部（`requesting-code-review`、`receiving-code-review`、`dispatching-parallel-agents`、`systematic-debugging`＋3 refs）。

## Current Scope

已進入 `main`:

- `docs/i18n/` 全部文件
- Phase 1、Phase 2、Phase 3 全部 skill

目前分支尚未合併（Phase 4 第一批）:

- `skills/writing-skills/SKILL.md`（初譯,689 行）
- `docs/i18n/behavior-test-cases.md`（新增 WS-01–07 情境測試規格）

**writing-skills 後續子批（尚未翻譯）**:

- `anthropic-best-practices.md`（1150 行,Anthropic 官方指引的 vendored 副本——最大;需判定是否／如何在地化,注意 CLAUDE.md 對「compliance 改動」的立場,翻譯屬中立在地化而非改philosophy）
- `testing-skills-with-subagents.md`（384 行,測試方法論 reference）
- `persuasion-principles.md`（187 行,研究基礎 reference）
- `examples/CLAUDE_MD_TESTING.md`（189 行,範例）
- **不譯（程式碼）**:`graphviz-conventions.dot`（171,graphviz 原始檔）、`render-graphs.js`（168,JS）

## 本批翻譯不變式（自查已過）

- description 加中英雙段,分隔用「`. `」。
- 鐵律雙語框:「沒有先寫一個會失敗的測試，就不得寫任何 skill / NO SKILL WITHOUT A FAILING TEST FIRST」（英文行逐字）。
- **全部 42 個 fenced marker 數量與原文一致**;所有 ``` 區塊保留英文原樣——含:markdown skill 模板、yaml ❌BAD／✅GOOD description 範例、bash（含 `wc -w`、`--help`）、壓縮範例區塊（其英文字數 42／20 是示範重點,絕不可改）、2 個 dot 圖、目錄樹、`<Bad>`／`<Good>` 範例、合理化表範例、Red Flags 範例。
- 3 個內容表格（TDD 對應、略過測試合理化 8 列、讓形式對應失敗 4 列）翻譯內文。
- mandatory anchor:必要背景（REQUIRED BACKGROUND）、必須（MUST）、絕對不要（NEVER）、只（ONLY）、關鍵（CRITICAL）、強制（MANDATORY）、停下（STOP）。
- skill ID（`superpowers:*`）、reference 檔名、`agentskills.io/specification`、citation `(Cialdini, 2021; Meincke et al., 2025)`、RED-GREEN-REFACTOR／TDD／YAGNI／SDO 保留。
- 「your human partner」:prose 一處→「你合作的人類使用者」;壓縮範例 ``` 區塊內一處保留英文（示範字數）。

## Current Task

### Owner

Claude／Anthropic

### Task Mode

`translate`（本輪已由使用者授權）

### Assignment

初譯 `writing-skills` 的 SKILL.md 本體並補情境測試規格;4 個大型 reference 與程式碼留給後續子批。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 不以 Anthropic 官方 skill guidance 重構 Superpowers 原有內容（翻譯 ≠ 改 philosophy）。
- 所有 fenced 區塊（尤其壓縮範例的英文字數、模板、dot、命令）皆不可改。
- `.dot`／`.js` 為程式碼,不在本批範圍。
- description 中英段刻意不逐字對稱為合規（translation-guide §2）。

## Resume Target

1. 獨立語意 review（未參與初譯的 reviewer 做中英逐段對照,特別檢查 42 個 fenced 區塊逐字性與壓縮範例字數）。
2. 執行實際 harness 行為 eval:依 `behavior-test-cases.md` 的 WS-01–07 跑中文觸發、壓力、反例與英文回歸。**本 session 無法執行**——`evals/` 未 clone,需 tmux 環境。
3. 使用者決定是否為本批開 PR 並合併。
4. writing-skills 後續子批:`testing-skills-with-subagents.md`、`persuasion-principles.md`、`examples/CLAUDE_MD_TESTING.md`、`anthropic-best-practices.md`（最大,可能單獨一批;或與使用者確認是否要譯此 vendored 官方文件）。之後平台 reference、README／install。

## Backlog（跨檔待整理，非本批 blocker）

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`、`harness`、`checkpoint` 等在多個 skill 以裸英文出現。建議累積後一次統一整理。
- 全形／半形逗號跨檔不一致:PR #3 起多用半形「,」。列為專門 typography 批次（只改散文、跳過 ``` 區塊）。
- description 分隔符:多數用「`. `」,`subagent-driven-development`（PR #7）用全形「。」無空格。留待 typography 掃描一併正規化。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: 初譯 Phase 4 第一批（`writing-skills` 的 SKILL.md 本體）並補情境測試規格
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支（尚未開 PR）
