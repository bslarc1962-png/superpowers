# zh-TW 行為等價 Eval 任務

## Status

- Task mode: `test`
- Owner model: Claude Code 作為 orchestrator／reviewer
- Execution model: Codex 處理機械盤點、fixture 初稿與批次執行
- Skill 正文修改：**本任務禁止**。發現 regression 時另開獨立修正 PR。

## Purpose

zh-TW 在地化已完成，`docs/i18n/behavior-test-cases.md` 也已定義 A–E 五層測試，但目前仍缺少可由本機重新執行、比較與交接的行為等價 workflow。

本任務把語意／行為等價測試留在 `superpowers` fork：

- `superpowers` 保存翻譯、來源、測試案例、transcript 與判定結果。
- ARSS 只在未來引用結果摘要與 pinned commit，不承擔 eval harness 或逐案測試細節。

第一輪只驗證三個已被 ARSS 借鑑的 skill：

1. `test-driven-development`
2. `systematic-debugging`
3. `verification-before-completion`

判準不是中文是否自然，而是翻譯前後在相同壓力下是否維持相同的：

- trigger
- phase gate／步驟順序
- 強制程度
- 例外範圍
- stop condition
- completion claim boundary

## Pinned Sources

- Upstream baseline: `obra/superpowers @ d884ae04edeb`
- zh-TW fork baseline: `bslarc1962-png/superpowers @ 2b64d350b4ad10db32fef382604cd59c98c7ccc7`
- Test specification: `docs/i18n/behavior-test-cases.md`
- Translation rules: `docs/i18n/translation-guide.md`
- Handoff rules: `docs/i18n/agent-handoff.md`

開始執行時若 baseline 已移動，先記錄新的 commit；不要用「最新版」作為來源身份。

## Required Warm-up

Claude Code 開始前依序讀：

1. `AGENTS.md`
2. `docs/i18n/agent-handoff.md`
3. `docs/i18n/NOW.md`
4. 本文件
5. `docs/i18n/behavior-test-cases.md` 中三個目標 skill 的案例
6. 三個目標 skill 的 upstream 與 zh-TW 正文

不要預設掃描整個 repo。

## Agent Roles

### Claude Code — Orchestrator／Reviewer

- 建立 branch／worktree 與階段性計畫。
- 判斷 eval harness 的正確接線方式。
- 將大量、可機械化的工作委派 Codex。
- Review Codex 產物與每個 failure classification。
- 不因單次模型輸出不同，就直接判定 behavioral drift。
- 整理 report、未解問題與修正建議。

### Codex — Mechanical Executor

適合委派：

- upstream／fork 結構與 invariant 比對。
- 將既有 Markdown cases 轉成 harness fixture 初稿。
- 執行重複 session，收集 transcript／judge output。
- 建立結果矩陣與可重跑命令。

Codex 不自行修改 skill 正文；發現問題只輸出 finding。

## Phase 0 — Inventory Only

先只做環境盤點，不執行大規模 eval。

1. 確認現有 eval harness、repo 測試結構與官方說明。
2. 確認 `superpowers-evals` 如何 clone／pin 到本 repo 的 `evals/`。
3. 確認 `.gitignore`、tmux、Claude Code／Codex CLI、模型憑證與 judge requirements。
4. 釐清哪些產物進 Git，哪些 transcript／cache 只留本機。
5. 估計單一 case 與整輪執行的 session／token 成本。

Phase 0 交付：

- 可執行命令
- 所需環境
- 成本與用量風險
- 本機檔案與 Git 產物邊界
- Phase 1 的最小 implementation plan

此階段不得修改 skill 正文，也不得直接跑完整案例矩陣。

## Phase 1 — Reproducible Harness Bootstrap

建立一個最小 smoke test，證明 harness 可以：

1. 啟動乾淨 session。
2. 載入指定英文或 zh-TW skill。
3. 送入固定 prompt。
4. 保存完整 transcript。
5. 產生可 review 的 judge result。

英文 baseline 與中文 candidate 必須使用相同 task fixture 與判定維度。

交付一條新 session 可直接重跑的命令與最小說明。

## Phase 2 — Normalize Target Cases

只轉換既有案例，不新增整套方法論：

- `TDD-01`～`TDD-07`
- `SD-01`～`SD-07`（以現有文件實際案例為準）
- `VBC-01`～`VBC-06`

每個 executable case 至少包含：

- case ID
- prompt language
- expected trigger
- required behaviors
- forbidden behaviors
- terminal state
- pass／fail 所需證據

規格本身含糊時標為 `spec-ambiguous`，不要強迫 judge 猜答案。

## Phase 3 — Paired Behavior Runs

每個案例至少比較：

1. upstream English skill + English prompt
2. zh-TW skill + Chinese prompt
3. zh-TW skill + English regression prompt（適用時）

同一組重跑多次，避免把 stochastic variance 當成翻譯漂移。實際次數由 orchestrator 根據成本與變異決定，並記錄理由。

結果分類固定為：

- `equivalent`
- `candidate-regression`
- `harness-or-judge-noise`
- `spec-ambiguous`
- `upstream-behavior-unstable`

## Phase 4 — Review and Repair Proposal

對每個 `candidate-regression`：

1. Claude 回看完整 transcript、英文原文與中文譯文。
2. 判斷問題屬於 trigger、gate、強度、例外、stop condition 或 judge。
3. 依 `docs/i18n/agent-handoff.md` 的 finding 格式記錄。
4. 若確認需修改 skill，另開單一問題、單一 skill 的獨立 PR。

本 eval 任務不得順手批次重寫技能內容。

## Known Finding Outside First-round Scope

ARSS 的 Level A review 曾指出 `brainstorming` description 可能遺失 MUST 強制語意。

這不是第一輪三個目標 skill 的 blocker；harness 成立後可作額外 smoke／regression case，但不得因此擴大第一輪核心範圍。

## Deliverables

最低交付：

1. 本機 eval setup／runbook。
2. 三個 skill 的 executable case fixtures。
3. paired run result matrix。
4. transcript／judge evidence 的可追溯位置。
5. review report：哪些等價、哪些疑似 regression、哪些無法判斷。
6. 更新 `docs/i18n/NOW.md` 的 resume target；不要把完整歷史搬進 NOW。

## Acceptance Criteria

- 新的 Claude Code／Codex session 只讀本文件與 repo 指標，就能重跑至少一個 case。
- 英文與 zh-TW 使用同一判定維度，不靠人工印象比較。
- failure 有 transcript 或 judge evidence，不以「模型感覺不同」作結論。
- eval workflow 與 skill 修正分離。
- 不修改 ARSS；ARSS 未來只引用結果摘要與 pinned commit。
- 所有新增檔案、依賴與 ignore 規則都有清楚理由。

## Non-goals

- 不一次驗證全部 14 個 skills。
- 不把 ARSS execution discipline 當成 judge 的標準答案。
- 不為了讓測試通過而弱化上游原本的強制語意。
- 不向 `obra/superpowers` 上游開 PR。
- 不先建立長期 CI／排程；先證明本機可重跑，再決定是否自動化。

## Local Start Prompt

```text
請讀 AGENTS.md、docs/i18n/agent-handoff.md、docs/i18n/NOW.md 與 docs/i18n/behavior-eval-task.md。你是 orchestrator：先只完成 Phase 0 inventory，將機械盤點與檔案比較委派給 Codex；不要修改 skill 正文，也不要直接進入大規模 eval。完成後留下可 review 的執行計畫與環境缺口。
```
