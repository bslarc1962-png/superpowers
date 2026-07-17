# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

面向終端中文使用者的文件在地化全部完成並合併（PR #1–#16）。本批（D）新增 Mermaid 流程圖對照頁，讓 GitHub 上 review 的中文使用者能 inline 看到各 skill 的流程圖。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（每批合併後從最新 `main` 重開）
- Base: `main`
- Previous delivery: PR #1–#16 全部合併。

## 本批範圍（D：Mermaid 流程圖對照頁）

- 新增 `docs/i18n/skill-flowcharts.md`——把 7 支 skill 內的 9 張 ` ```dot `（Graphviz）流程圖，以 GitHub 原生可渲染的 Mermaid 重畫，供在 GitHub review 的中文使用者 inline 檢視。
- 涵蓋：brainstorming、writing-skills、test-driven-development、subagent-driven-development（when + process）、dispatching-parallel-agents、root-cause-tracing（when + principle）、condition-based-waiting。

## 本批不變式（自查已過）

- **canonical 零改動**：所有 `skills/**/SKILL.md` 及子檔的 ` ```dot ` 區塊完全未動——render-graphs.js 工具、writing-skills 慣例、上游 fidelity、agent 執行時讀到的內容皆不受影響。本頁是**純粹的 review 輔助**，非 canonical、非 agent 行為內容。
- **忠實對照**：節點/邊/標籤與 canonical .dot 一一對應;節點文字譯中文以利吸收，但 skill ID、檔案路徑、技術術語（`RED`／`GREEN`／`REFACTOR`、`setTimeout`／`sleep`、`defense-in-depth`、`spec ✅`、`Critical/Important` 等）保留原文。dispatching 中 canonical 已定義但無連線的 orphan 節點如實保留並註明。
- **Mermaid 語法 lint 已過**：9 個 block 括號/引號/subgraph 配對平衡;避開保留字節點 id（`start`→`p0`）;決策節點 `{}`、subgraph、classDef、`<br/>` 皆為 GitHub Mermaid 標準支援構造。
- 頁首明載：若本頁與某 `SKILL.md` 的 ` ```dot ` 有出入，以 `SKILL.md` 為準。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- canonical skill（含其 ` ```dot ` 流程圖）逐字保留、零改動。
- 這是在地化與 review 輔助，非改動專案內容或 rebrand。

## Resume Target

zh-TW 在地化專案實質完成：
- 核心 skill＋行為鏈 reference（PR #1–#13）
- typography sweep（#14）
- README／安裝說明（#15）
- 移植開發者指南（#16）
- Mermaid 流程圖對照頁（本批 D）

## Backlog（非 blocker，皆為可選）

- glossary 首次出現 gloss 慣例執行寬鬆（`baseline`、`harness`、`checkpoint` 等裸英文）。術語一致性，可另批。
- `anthropic-best-practices.md`（1150 行）刻意未在地化、`.dot`／`.js` 程式碼不譯。
- 行為 eval（`behavior-test-cases.md`）仍需 tmux 環境，`evals/` 未 clone。durable 已知缺口。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: Mermaid 流程圖對照頁（`docs/i18n/skill-flowcharts.md`）
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支
