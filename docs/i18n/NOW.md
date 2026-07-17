# zh-TW NOW

本文件只保存目前中文化工作的短期狀態與下一個 durable resume target。歷史細節由 Git 與 PR 保存。

## Current Mode

核心 skill 全數在地化已完成並合併（PR #1–#13,涵蓋 14 個 skill 與全部行為鏈 reference）。目前進入收尾階段:(A) typography 一致性 sweep;(B) README／安裝說明在地化。本批為 (A)。

## Active Branch / PR

- Branch: `claude/pr2-review-zh-localization-koxhdq`（每批合併後從最新 `main` 重開）
- Base: `main`
- Previous delivery: PR #1–#13 全部合併。

## 本批範圍(A：typography sweep,純散文正規化)

目標:與既有主流風格一致。量測顯示冒號（474 全形 : 139 半形）、括號（427 : 37）早已以全形為主,只有逗號漂移成半形（264 全形 : 580 半形）。故本批把「緊鄰漢字」的半形逗號、冒號正規化為全形,並統一 `description` 分隔符。

- 正規化 `skills/**/*.md`＋`docs/i18n/*.md` 中 fenced 區塊外、inline code 外、緊鄰漢字的半形 `,`→`，`、`:`→`：`(共 645 處,22 檔)。
- `subagent-driven-development/SKILL.md` 的 `description` 分隔符 `。`→`. `,與其餘 13 個 skill 一致。
- `translation-guide.md`:§5 新增「標點用全形」durable 慣例;review checklist 補 2 項。

**不動**:括號（成對配對風險、量少 37,且（English）錨點本就全形）;半形標點保留於英文、程式碼、inline code、URL、數字（`N=28,000`）與 fenced 區塊。

## 本批不變式(自查已過)

- 每個變動檔的「所有 fenced 區塊內容」working-tree vs `HEAD` **byte-identical**（awk 抽取＋字串比對,ALL FENCES BYTE-IDENTICAL）。純散文標點置換,未動任何 specimen／dispatch payload／dot／yaml／測試受測資料。
- 殘餘「緊鄰漢字的半形逗號／冒號(fence 外)」＝ 0。
- 14 個 SKILL.md 的 `description` 分隔符皆為「`. `」。
- 無 `，，`／`::`／`：：` 等重複標點。

## Boundaries

- 不修改根目錄 `AGENTS.md` 或 `CLAUDE.md`。
- 不把本 fork 的中文化內容提交到上游。
- 只改散文標點,不改任何字詞、行為、gate、順序、終止狀態。
- fenced 區塊、inline code、英文、程式碼、URL、數字內的半形標點不動。

## Resume Target

1. 本批(typography)合併後,執行 (B):在地化 README／安裝說明。
   - 主目標:`README.md`(273)、`docs/README.kimi.md`(88)、`docs/README.opencode.md`(163)——皆為安裝／使用說明,對中文使用者直接有用。
   - 次要:`docs/testing.md`(35,貢獻者測試說明)、`docs/porting-to-a-new-harness.md`(827,harness 移植開發指南——非安裝文件,量大,視情況另批)。
   - 規則:所有安裝命令、路徑、URL、程式碼、harness 名稱逐字保留;新譯內容一開始就用全形標點。
2. 行為 eval(`behavior-test-cases.md`)仍無法在本 session 執行——需 tmux 環境,`evals/` 未 clone。durable 已知缺口。

## Backlog(非本批 blocker)

- glossary 首次出現 gloss 慣例執行寬鬆:`baseline`、`harness`、`checkpoint` 等在多個 skill 以裸英文出現。屬術語一致性(非 typography),另批處理。
- `anthropic-best-practices.md`(1150 行)刻意未在地化(使用者裁示「對我沒幫助就不用譯」)、`.dot`／`.js` 程式碼不譯。

## Last Handoff

- Handled by: Claude／Anthropic
- Date: 2026-07-17
- Task: typography 一致性 sweep(全形標點正規化＋description 分隔符統一)
- Required first read: `docs/i18n/agent-handoff.md`
- Current discussion surface: 目前分支
