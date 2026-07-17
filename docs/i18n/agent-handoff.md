# zh-TW Agent 交接協定

本文件定義多個 agent 在 Superpowers 繁體中文化工作中如何接棒。

目標是讓使用者只需指出一個入口，不必重新敘述背景、規劃與已完成內容。

## 核心原則

1. **Repo 保存 durable context**：長期規則、術語、進度與驗收條件寫入 repo，不依賴聊天記憶。
2. **使用者只負責指路**：交接時只需指定 `docs/i18n/NOW.md` 與相關 PR／issue。
3. **穩定規則與當前任務分離**：
   - 穩定方法放在 `translation-guide.md`、`glossary.md` 與本文件。
   - 目前進度、分支、下一步與負責 agent 放在 `NOW.md`。
   - 討論、review findings 與修改紀錄留在對應 PR。
4. **不覆寫上游 adapter**：根目錄的 `AGENTS.md`、`CLAUDE.md` 等屬於上游專案規則。中文 fork 的工作規則放在 `docs/i18n/`，降低 upstream sync 衝突。
5. **交接不等於授權**：下一位 agent 必須從 `NOW.md` 確認任務模式是 review、edit、test 或 deliver，不自行擴大範圍。

## Agent 暖機順序

進入中文化任務時依序讀：

1. 根目錄 `AGENTS.md`：理解上游貢獻與 skill 變更要求。
2. `docs/i18n/README.md`：理解中文化目標與文件地圖。
3. `docs/i18n/NOW.md`：取得目前分支、PR、任務、權限與 resume target。
4. `docs/i18n/translation-guide.md`：翻譯與 review 規則。
5. `docs/i18n/glossary.md`：固定術語。
6. 只讀本次任務直接相關的 skill、reference 與 PR diff；不要預設掃描整個 repo。

## 任務模式

`NOW.md` 的 `Task Mode` 必須是下列其中一種：

| Mode | 可以做什麼 | 不可以做什麼 |
|---|---|---|
| `review-only` | 比對原文與翻譯、留下 findings | 不修改檔案、不擴充翻譯範圍 |
| `patch-approved` | 修正已確認的 findings | 不新增未討論的設計改造 |
| `translate` | 翻譯指定 skill／reference | 不處理未列入 scope 的檔案 |
| `test` | 執行情境測試、記錄結果 | 不以測試名義順手重寫內容 |
| `deliver` | 整理 diff、checks 與 PR | 未取得使用者授權時不 merge |

若 `NOW.md` 未指定 mode，預設為 `review-only`。

## Review finding 格式

Claude 或其他 reviewer 應將問題分類為：

1. **Behavioral drift**：gate、步驟、優先順序、終止條件或強制程度改變。
2. **Missing invariant**：原文中的 ID、路徑、命令、數量限制或控制詞遺失。
3. **Terminology inconsistency**：違反 `glossary.md` 或同一詞多種譯法。
4. **Readability**：中文不自然、難懂，但不影響行為。
5. **Upstream question**：原文本身疑似矛盾或需要另案處理。

每個 finding 至少包含：

```markdown
- Severity: blocker | major | minor | note
- Category:
- File / section:
- English invariant:
- Current Chinese behavior:
- Suggested direction:
```

Review findings 優先留在 PR comment 或 inline review，不另外複製進多份文件。

## 交付責任

任務完成時，執行 agent 應：

1. 在 PR 留下完成摘要、驗證結果與未解問題。
2. 更新 `docs/i18n/NOW.md`：
   - 已完成項目
   - 下一個 resume target
   - 下一位 agent 的建議 mode
   - 尚未取得的授權
3. 不把完整歷史複製到 `NOW.md`；歷史由 Git 與 PR 保存。
4. 只有可跨任務重用的操作經驗，才更新本文件或 `translation-guide.md`。

## 使用者的最小交接語句

一般 review：

```text
請先讀 docs/i18n/NOW.md，依其中的任務模式接手目前中文化工作，並把結果留在指定 PR。
```

指定 Claude review：

```text
請讀 docs/i18n/NOW.md 與指定 PR，執行其中的 Claude review 任務；先只 review，不要修改檔案。
```

這一行指令是必要的啟動指標，但使用者不需要重述專案背景、翻譯規則或已完成內容。
