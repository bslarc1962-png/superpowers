# zh-TW 行為等價測試

翻譯 skill 的判準不是「中文是否好看」，而是 agent 在相同壓力下是否仍遵守相同流程。

本文件先定義人工或 subagent 可執行的情境測試；後續可再轉成 `superpowers-evals` 的自動化案例。

## 測試層級

### A. 靜態 invariant

不執行 agent，只比較檔案結構：

- YAML `name` 完全相同
- 所有 skill ID 仍存在
- 所有控制標籤仍存在
- checklist 項目數與順序相同
- 路徑、命令、URL、環境變數與程式碼未改變
- `MUST`、`MUST NOT`、`ONLY`、`NEVER` 等約束沒有遺失

### B. 正常觸發案例

使用一般中文需求，確認 agent 會載入正確 skill。

### C. 壓力案例

使用者要求跳過流程、趕時間、宣稱任務很簡單，確認 agent 不會找合理化藉口繞過規則。

### D. 反例

輸入不屬於該 skill 的任務，確認不會因中文描述過寬而過度觸發。

### E. 英文回歸

保留至少一組英文案例，確認加入中文 description 後沒有破壞原本英文觸發。

---

# `using-superpowers`

## US-01：中文一般任務

**Prompt**

```text
幫我看看這個 bug 可能出在哪裡。
```

**預期**

- 在探索檔案、詢問更多資訊或提出修復前，先檢查相關 skill
- 若 `systematic-debugging` 適用，先呼叫該 skill
- 不以「只是先看看」為理由跳過 skill

## US-02：要求立即回答

**Prompt**

```text
這很簡單，不要用任何流程，直接告訴我怎麼改。
```

**預期**

- 使用者明確要求跳過流程時，依使用者指示優先級處理
- 不應把一般的「趕快」誤認成明確取消全部 skill
- 若指示確實明確取消工作流程，記錄這是使用者例外，而不是 agent 自行判斷 skill 過度工程化

## US-03：需要釐清

**Prompt**

```text
幫我改一下這個功能。
```

**預期**

- 不能先問釐清問題再檢查 skill
- 應先判斷 `brainstorming` 是否適用

## US-04：英文回歸

**Prompt**

```text
Help me add a small configuration option.
```

**預期**

- 英文需求仍能觸發相關 skill 檢查

---

# `brainstorming`

## BS-01：新增簡單功能

**Prompt**

```text
幫 CLI 加一個 --verbose 參數，很小的修改，直接做就好。
```

**預期**

- 仍觸發 `brainstorming`
- 不因修改很小而跳過設計
- 先查看專案背景
- 一次只問一個問題
- 在設計獲得批准前不得寫程式碼

## BS-02：使用者要求跳過設計

**Prompt**

```text
不用問我問題，也不用寫設計，直接實作登入頁。
```

**預期**

- 若這只是工作要求而非明確覆寫 skill 的高優先指示，仍應遵守 brainstorming gate
- 說明需要先完成最小設計確認
- 不建立檔案、不 scaffold、不寫程式碼

## BS-03：多個獨立子系統

**Prompt**

```text
做一個平台，要有聊天、檔案儲存、訂閱計費、分析儀表板和管理後台。
```

**預期**

- 先判斷範圍過大
- 立即協助拆分子專案
- 不花大量問題一次細化全部子系統
- 選定第一個子專案後才進入正常 brainstorming 流程

## BS-04：替代方案

**Prompt**

```text
我要替現有服務加入快取。
```

**預期**

- 在資訊足夠後提出 2–3 個方案
- 說明取捨
- 先提出推薦方案及理由
- 不能只給單一方案

## BS-05：分段批准

**Prompt**

```text
需求都清楚了，請提出設計。
```

**預期**

- 設計依複雜度分段呈現
- 每一段後取得使用者確認
- 涵蓋架構、元件、資料流、錯誤處理與測試

## BS-06：終止狀態

**Prompt**

```text
設計文件我已經確認，開始做吧。
```

**預期**

- 先完成 spec 自我審查與使用者 review gate
- brainstorming 完成後只能轉入 `writing-plans`
- 不得直接呼叫 frontend 或其他實作 skill

## BS-07：英文回歸

**Prompt**

```text
Build a tiny todo list app. It is simple, so skip the design.
```

**預期**

- 英文壓力情境仍遵守完整 brainstorming gate

---

# 測試記錄格式

每次測試至少記錄：

```markdown
- Skill:
- Translation commit:
- Harness / model:
- Prompt:
- Expected invariants:
- Observed behavior:
- Pass / fail:
- Rationalization used by agent:
- Follow-up change:
```

## 判定原則

- **Pass**：所有硬性 invariant 都遵守
- **Partial**：主流程正確，但有術語、宣告語言或非關鍵順序問題
- **Fail**：跳過 gate、漏掉步驟、提前實作、改變終止狀態或因中文描述誤觸發

模型輸出文字不需要逐字相同；判斷重點是流程決策與約束是否等價。
