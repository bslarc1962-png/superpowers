# zh-TW 中文化 Roadmap

中文化採小批次、可審查、可回退的方式進行。排序依據不是檔案長度，而是 skill 在整體工作流程中的位置與控制風險。

## Phase 0：建立本地化基礎

目標：任何 agent 都能在不依賴口頭脈絡的情況下接手。

- [x] 建立本地化入口文件
- [x] 建立翻譯規範
- [x] 建立固定術語表
- [x] 建立行為測試規格
- [ ] 決定長期 branch 與 upstream 同步策略
- [ ] 補上自動化 invariant checker

### Branch 策略待驗證

在確認各平台是否能從非 default branch 安裝前，不急著永久改變 branch 結構。

候選方案：

1. `main` 保持中文使用版，另設 `upstream-sync` 保存純上游版本
2. `main` 跟隨上游，長期 `zh-tw` branch 保存中文使用版
3. `main` 跟隨上游，中文化以獨立 package 或 build 產物發布

第一批翻譯先放在 feature branch，完成實際安裝 smoke test 後再決定。

## Phase 1：入口與設計流程

這一階段決定 agent 是否會使用 skills，以及是否會先設計再實作，風險最高。

- [x] `using-superpowers`
- [x] `brainstorming`
- [ ] `writing-plans`
- [ ] `using-git-worktrees`

驗證重點：

- 中文需求是否能觸發正確 skill
- agent 是否仍在任何動作前檢查 skill
- agent 是否仍遵守設計批准 gate
- 是否保持「一次只問一個問題」
- `brainstorming` 的終止狀態是否仍為 `writing-plans`

## Phase 2：實作與驗證主迴圈

- [ ] `test-driven-development`
- [ ] `executing-plans`
- [ ] `subagent-driven-development`
- [ ] `verification-before-completion`
- [ ] `finishing-a-development-branch`

驗證重點：

- RED-GREEN-REFACTOR 順序不可改變
- 不得在沒有看到測試失敗前寫正式程式碼
- subagent 的規格審查與程式品質審查不可合併
- 宣稱完成前必須有新鮮驗證證據

## Phase 3：除錯與協作

- [ ] `systematic-debugging`
- [ ] `dispatching-parallel-agents`
- [ ] `requesting-code-review`
- [ ] `receiving-code-review`

驗證重點：

- 根因分析優先於猜測修補
- 平行任務必須彼此獨立
- review feedback 不得盲目接受或情緒化拒絕

## Phase 4：Meta 與參考資料

- [ ] `writing-skills`
- [ ] 平台 reference：Codex、Pi、Antigravity 等
- [ ] Visual Companion 文件
- [ ] testing anti-patterns 與其他大型 reference
- [ ] README 與安裝文件

這階段檔案較長，適合交由第二個 agent 初譯，再由熟悉核心規則的 reviewer 審查。

## PR 切分原則

每個 PR 建議只包含 1–3 個具直接關聯的 skill：

- PR A：本地化規範 + `using-superpowers` + `brainstorming`
- PR B：`writing-plans` + `using-git-worktrees`
- PR C：`test-driven-development` + `verification-before-completion`
- PR D：`executing-plans` + `subagent-driven-development`
- PR E：code review 相關 skills
- PR F：systematic debugging 相關內容

不要把所有 skill 一次翻完後才開始 review。一次性大翻譯很難判斷行為偏差是在哪一個檔案引入。

## 多 agent 分工建議

### Agent A：核心控制語意

適合由主導者處理：

- `using-superpowers`
- `brainstorming`
- `writing-plans`
- `test-driven-development`
- 翻譯規範、術語表與行為測試

這些 skill 的 `MUST`、gate、終止條件與優先順序最容易因翻譯改變行為。

### Agent B：初譯與 reference 整理

適合交給 Claude 或另一個 agent：

- 大型 reference 文件
- 平台適配說明
- README 與安裝文件
- code review、Git worktree 等較具體流程

輸出要求：

1. 不修改 skill ID、路徑、程式碼與命令
2. 依照 `translation-guide.md`
3. 遇到術語不確定時先加入候選清單，不自行創造多套譯法
4. 每個 skill 附上「不可變行為摘要」

### Agent C：獨立 reviewer

reviewer 不應審查自己剛完成的初譯。主要工作：

- 中英文逐段對照
- 檢查是否遺漏 gate、ONLY、NEVER 或 checklist
- 搜尋術語不一致
- 執行壓力情境
- 檢查是否因中文變得更寬鬆或更武斷

## 建議給 Claude 的任務模板

```text
請依照 docs/i18n/translation-guide.md 與 docs/i18n/glossary.md，
將 <skill-path> 初譯為台灣繁體中文。

要求：
1. name、skill ID、路徑、命令、程式碼與控制標籤保持原樣。
2. description 採英文觸發條件 + 中文同義觸發條件，且只描述何時使用。
3. 不改變流程步驟、gate、數量限制、終止條件與下一個 skill。
4. 先列出不可變行為摘要，再進行翻譯。
5. 完成後依 translation guide 的 review checklist 自查。
6. 不順手修正原版方法論；發現問題另列 notes。
```

## 完成定義

一個 skill 只有同時符合下列條件才算完成：

- 中文正文可獨立閱讀
- frontmatter 符合規範
- 術語一致
- 行為限制與原文等價
- 中文觸發案例通過
- 壓力案例通過
- 至少一個英文回歸案例通過
- 由第二位 reviewer 審查
