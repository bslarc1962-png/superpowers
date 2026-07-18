# Superpowers 繁體中文化

本目錄是 Superpowers 的繁體中文（zh-TW）本地化入口。

這個專案的目標不是把英文逐字替換成中文，而是讓使用者能夠真正讀懂、檢查並學習 Superpowers 的工作方法，同時盡量維持原版 skill 的觸發條件、約束強度與流程行為。

## 核心目標

1. **降低閱讀成本**：正文以繁體中文為主，讓工作流不再是黑盒。
2. **保持行為等價**：翻譯後的 agent 應盡量做出與英文原版相同的流程決策。
3. **保留生態相容性**：skill 名稱、路徑、命令、程式碼與控制標籤維持英文原樣。
4. **可持續同步上游**：翻譯、上游同步與行為調整分開提交，降低日後合併成本。
5. **讓翻譯本身可驗證**：核心 skill 必須透過情境測試確認，而不能只靠文字校對。
6. **讓 agent 能直接接棒**：目前狀態與任務邊界由 repo 保存，不依賴使用者重述聊天內容。

## 接手目前工作

Agent 接手中文化任務時先讀：

1. [NOW.md](NOW.md)：目前分支、PR、任務模式、scope 與 resume target
2. [agent-handoff.md](agent-handoff.md)：暖機順序、權限模式與交付方式

一般使用者只需給下一個 agent 一句話：

```text
請先讀 docs/i18n/NOW.md，依其中的任務模式接手目前中文化工作。
```

## 文件地圖

1. [translation-guide.md](translation-guide.md)：翻譯規則與審查清單
2. [glossary.md](glossary.md)：固定術語與保留字
3. [roadmap.md](roadmap.md)：翻譯順序、PR 切分與多 agent 分工（含完成狀態與剩餘項目）
4. [behavior-test-cases.md](behavior-test-cases.md)：行為等價測試案例
5. [agent-handoff.md](agent-handoff.md)：跨 agent 交接協定
6. [skill-flowcharts.md](skill-flowcharts.md)：各 skill 流程圖的 Mermaid 對照（GitHub inline review 用；canonical 仍為各 `SKILL.md` 的 ` ```dot `）
7. [NOW.md](NOW.md)：目前短期狀態與下一步

## 目前狀態

實質完成（2026-07,PR #1–#17 皆已合併）。逐項狀態見 [roadmap.md](roadmap.md)。

| 類別 | 狀態 |
|---|---|
| 中文化規則、術語表、交接協定、行為測試規格 | 完成 |
| 全部 14 支 skill（`SKILL.md`） | 完成 |
| 行為鏈 reference 子檔（root-cause-tracing、testing-anti-patterns、persuasion-principles 等） | 完成 |
| Typography 一致性 sweep | 完成 |
| README／各 harness 安裝說明 | 完成 |
| 移植開發者指南（`porting-to-a-new-harness.md`） | 完成 |
| Mermaid 流程圖對照頁（`skill-flowcharts.md`） | 完成 |
| `visual-companion.md`（視覺陪伴指南） | 完成 |
| subagent prompt 檔、`anthropic-best-practices.md`、工具對應 reference、`.dot`／`.js` | 刻意留英（見 roadmap 文末） |

## 基本策略

### 中文為主，不做逐段雙語

完整的中英對照會讓常駐 skill 的 token 成本接近翻倍。因此正文以中文為主，只在下列位置保留英文錨點：

- skill ID 與資料夾名稱
- YAML frontmatter 的 `name`
- `MUST`、`MUST NOT`、`Do NOT` 等關鍵強制詞
- `<HARD-GATE>`、`<EXTREMELY-IMPORTANT>` 等控制標籤
- 命令、參數、檔案路徑、程式碼與專有名詞
- 容易因翻譯產生歧義的工作流術語

### 小批次翻譯

每個 PR 原則上只處理 1–3 個相關 skill，並包含：

- 原文語意比對
- 術語一致性檢查
- 觸發條件檢查
- 至少一組中文壓力情境
- 必要時的英文回歸情境

### 翻譯與改造分離

若發現原版規則有矛盾、過時或可改善之處，先記錄成獨立議題。除非是修復明顯的中文歧義，不在翻譯 PR 中順便重新設計工作流。

## 上游與授權

本專案基於 Superpowers，上游專案與授權資訊以 repository 根目錄的 `README.md` 與 `LICENSE` 為準。中文化內容應保留原作者署名與授權資訊。