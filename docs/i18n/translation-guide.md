# zh-TW 翻譯規範

本文件定義 Superpowers skill 的繁體中文化規則。優先順序如下：

1. 行為等價
2. 控制強度不變
3. 觸發條件可被中英文需求正確找到
4. 中文自然、容易閱讀
5. 盡量降低與上游同步的衝突

## 1. 不可翻譯的內容

下列內容原則上必須保持原樣：

- YAML `name`
- skill 資料夾與檔案名稱
- skill 之間的引用名稱，例如 `writing-plans`
- XML-like 控制標籤，例如 `<HARD-GATE>`
- CLI 命令、參數與環境變數
- 程式碼、正規表示式、錯誤訊息
- 檔案路徑與 URL
- Mermaid、Graphviz、JSON、YAML 等機器可讀內容中的識別字
- `RED-GREEN-REFACTOR`、`TDD`、`YAGNI`、`DRY` 等既有術語

若流程圖中的顯示文字只是給人閱讀，可以翻譯；節點 ID、路徑、skill ID 與程式識別字不可改。

## 2. Frontmatter 規則

### `name`

完全保留英文：

```yaml
name: brainstorming
```

### `description`

採短雙語格式，僅描述「何時應載入這個 skill」，不可濃縮或提前透露完整工作流。

```yaml
description: "Use before any creative work involving features, components, functionality, or behavior changes. 適用於新增功能、建立元件、加入能力或修改行為之前。"
```

要求：

- 英文觸發條件在前，中文同義觸發條件在後
- 保留使用者可能輸入的關鍵詞與症狀
- 不寫執行步驟、輸出結果或流程摘要
- 盡量控制在 500 字元內
- 不加入與原 skill 無關的新觸發條件

## 3. 強制語氣對照

| 英文 | 中文 | 使用規則 |
|---|---|---|
| MUST | **必須（MUST）** | 不可弱化成「應該」「建議」 |
| MUST NOT | **禁止（MUST NOT）** | 用於絕對禁止 |
| Do NOT | **不得（Do NOT）** | 保留英文錨點 |
| REQUIRED | **必要（REQUIRED）** | 不可翻成「推薦」 |
| NEVER | **絕對不要（NEVER）** | 保持絕對語氣 |
| ONLY | **只能（ONLY）** | 保持排他性 |
| may / can | 可以 | 不可翻成必須 |
| should | 應 | 不可翻成必須 |
| prefer | 優先 | 表示偏好而非硬性規則 |

同一段中不必每次重複英文；第一次出現或位於關鍵控制段落時保留即可。

## 4. 建議保留英文的術語

下列詞彙首次出現時使用「中文解釋（英文）」；後續可直接使用英文：

- skill
- agent / subagent
- harness
- worktree
- checkpoint
- spec
- implementation plan
- code review
- baseline
- refactor
- upstream

例如：

> 建立隔離工作目錄（Git worktree），並確認測試基準（baseline）乾淨。

不要把 `skill` 翻成「技能」、把 `agent` 翻成「代理人」，以免與框架 UI、文件名稱及使用者既有認知脫節。

## 5. 中文風格

- 使用台灣繁體中文
- 句子短而直接
- 一個 bullet 儘量只表達一個規則
- 優先使用主動語態
- 不增加激勵性口號、禮貌填充或文學修飾
- 保留原文的幽默，但不可讓規則變模糊
- `user` 統一譯為「使用者」
- `human partner` 可譯為「合作的人類使用者」或「使用者」，依上下文決定
- `codebase` 統一譯為「程式碼庫」
- `workflow` 統一譯為「工作流程」

## 6. 可以調整的內容

在不改變行為的前提下，可以：

- 拆分過長英文句子
- 將複雜段落改成條列
- 在專有名詞第一次出現時補上英文
- 把英文範例對話改為等價的中文對話
- 將宣告句調整為「使用者目前的語言」
- 修正明顯不自然、容易造成誤讀的直譯

## 7. 不可順手調整的內容

翻譯 PR 中不可未經說明地：

- 增減流程步驟
- 改變 gate 的前後順序
- 改變「一次一個問題」等數量限制
- 改變 skill 的終止狀態或下一個 skill
- 新增例外條款
- 改變測試要求
- 改變檔案輸出位置
- 改變 Git、commit 或 PR 行為
- 將強制規則改成建議

發現原文問題時，先記錄在 PR 說明或獨立 issue，再由另一個變更處理。

## 8. 每個 skill 的翻譯流程

1. 閱讀完整 skill 與其直接引用的 reference
2. 列出不可變行為：觸發、gate、順序、輸出、終止條件
3. 翻譯 frontmatter
4. 翻譯正文，保留識別字與控制標籤
5. 對照段落與 checklist，確認沒有遺漏
6. 執行中文觸發情境與壓力情境
7. 執行至少一個英文回歸情境
8. 由未參與初譯的 agent 或人工 reviewer 複核

## 9. Review checklist

- [ ] `name` 完全未變
- [ ] `description` 只描述觸發條件
- [ ] 重要中英文關鍵詞都有覆蓋
- [ ] `MUST`、`MUST NOT`、`ONLY` 等強度未改變
- [ ] 所有 checklist 項目都存在且順序一致
- [ ] 所有 gate 與終止條件都存在
- [ ] skill ID、路徑、命令、程式碼未被翻譯
- [ ] 沒有新增未經討論的例外
- [ ] 中文讀起來像操作規範，而不是翻譯腔
- [ ] 通過對應的行為測試案例
