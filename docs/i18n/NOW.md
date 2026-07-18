# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

zh-TW 在地化實質完成（PR #1–#17 皆已合併）。本批（E）為 i18n meta 文件收尾：把早期規劃文件更新為反映真實完成狀態，並如實盤點剩餘項目。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（每批合併後從最新 `main` 重開）
- Base: `main`
- Previous delivery: PR #1–#17 全部合併。

## 本批範圍（E：i18n meta 文件收尾）

- `docs/i18n/roadmap.md`——加上完成狀態 banner;Phase 0–4 全部 skill／文件項目勾選為完成;新增「剩餘與刻意未譯」段落，誠實盤點尚未在地化、刻意留英、與環境限制三類。
- `docs/i18n/README.md`——「目前狀態」表由過時（僅 2 支 skill 初譯）改寫為真實完成狀態;文件地圖補上 `skill-flowcharts.md`。

## 完整盤點（收尾自查結果）

- **已完成**：全部 14 支 `SKILL.md`;行為鏈 reference 子檔（root-cause-tracing、condition-based-waiting、defense-in-depth、testing-anti-patterns、persuasion-principles、testing-skills-with-subagents、code-reviewer、task-reviewer-prompt、plan-document-reviewer-prompt、CLAUDE_MD_TESTING）;typography sweep;README／安裝說明;移植指南;Mermaid 對照頁;全部 i18n meta 文件。
- **尚未在地化（可選，非 blocker）**:`skills/brainstorming/visual-companion.md`（291 行，使用者導向指南，0 中文）;`spec-document-reviewer-prompt.md`、`implementer-prompt.md`（subagent prompt 檔，行為敏感）。
- **刻意留英**:`anthropic-best-practices.md`（1150 行）、工具對應 reference（`references/*-tools.md`）、測試 fixture／`CREATION-LOG.md`、所有 ` ```dot `／`.js`。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- canonical skill（含其 ` ```dot ` 流程圖）逐字保留、零改動。
- meta 文件只反映真實狀態，不虛報完成。

## Resume Target

zh-TW 在地化專案收尾完成。唯一仍有價值的可選後續：在地化 `visual-companion.md`（使用者導向的視覺陪伴指南，291 行）。若要做，另開一批，流程同前（從 main 重開分支 → 譯 → 自查 → commit → PR → merge）。

## Backlog（非 blocker，皆為可選）

- `skills/brainstorming/visual-companion.md` 在地化（見上）。
- glossary 首次出現 gloss 慣例執行寬鬆（`baseline`、`harness`、`checkpoint` 等裸英文）。術語一致性，可另批。
- 行為 eval（`behavior-test-cases.md`）仍需 tmux 環境，`evals/` 未 clone。durable 已知缺口。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-18
- Task: i18n meta 文件收尾（roadmap.md＋README.md 更新為真實完成狀態）
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支
