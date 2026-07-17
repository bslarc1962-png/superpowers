# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

核心 skill 全數在地化已完成並合併（PR #1–#13）。收尾階段:(A) typography 一致性 sweep 已合併(PR #14);(B) README／安裝說明在地化——本批。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（每批合併後從最新 `main` 重開）
- Base: `main`
- Previous delivery: PR #1–#14 全部合併。

## 本批範圍(B：README／安裝說明在地化)

在地化對中文使用者直接有用的安裝／使用文件:

- `README.md`(273 行)——專案首頁、完整安裝說明(10 個 harness)、工作流程、skill 清單、理念、遙測、社群。
- `docs/README.kimi.md`(88 行)——Kimi Code 安裝／工具對應／疑難排解。
- `docs/README.opencode.md`(163 行)——OpenCode 安裝／使用／更新／工具對應／疑難排解。
- `docs/testing.md`(35 行)——貢獻者:兩類測試(`tests/`＋`evals/`)說明。

## 本批不變式(自查已過)

- 4 檔的**所有 fenced 區塊內容**working-tree vs `HEAD` **byte-identical**(安裝命令、JSON、bash、powershell、markdown 範例、YAML 皆逐字)。
- **錨點保留**:`README.md` 的 10 個 harness 標題(`### Claude Code` … `### Pi`)維持英文,Quickstart 的 `#claude-code` 等 TOC 連結不斷。
- **逐字保留**:所有安裝命令、URL、email、skill ID/名稱、工具名(`AskUserQuestion`、`task`、`Subagent (general-purpose):` 模板等,均在 inline code 內受保護)、`RED-GREEN-REFACTOR`／`TDD`／`YAGNI`／`DRY`、產品/公司名(Superpowers、Prime Radiant、作者署名)、`dev` 分支名。
- **全形標點**:散文逗號/冒號全形;英文字後的引導冒號(如 `安裝 Superpowers：`)、粗體標籤冒號(`**Discord**：`)亦轉全形以求一致;殘餘 Chinese-adjacent 半形逗號/冒號＝0。URL 內冒號、程式碼、數字不動。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 安裝命令、路徑、URL、程式碼、harness 名稱、錨點逐字保留。
- 這是在地化,非改動專案內容或 rebrand:保留所有連結、作者署名、商業資訊。

## Resume Target

核心 skill＋行為鏈 reference＋typography＋README/安裝說明皆已完成。剩餘的都是可選:

1. `docs/porting-to-a-new-harness.md`(827 行)——harness 移植**開發者指南**,非安裝文件、量大、對終端中文使用者價值較低。若要譯需另開批次。
2. 行為 eval(`behavior-test-cases.md`)仍無法在本 session 執行——需 tmux 環境,`evals/` 未 clone。durable 已知缺口。

## Backlog(非本批 blocker)

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`、`harness`、`checkpoint` 等在多個 skill 以裸英文出現。屬術語一致性,可另批。
- `docs/porting-to-a-new-harness.md` 未在地化(開發者指南,見上)。
- `anthropic-best-practices.md`(1150 行)刻意未在地化、`.dot`／`.js` 程式碼不譯。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: README／安裝說明在地化(README.md＋kimi＋opencode＋testing)
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支
