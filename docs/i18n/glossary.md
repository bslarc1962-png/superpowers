# zh-TW 術語表

本表用於維持不同 skill、不同 agent 與不同 PR 之間的翻譯一致性。

## 保留英文

| 英文 | 建議寫法 | 說明 |
|---|---|---|
| skill | skill | 不譯為「技能」，避免和一般能力混淆 |
| agent | agent | 不譯為「代理人」 |
| subagent | subagent | 必要時首次寫「子 agent（subagent）」 |
| harness | agent 執行框架（harness） | 指 Claude Code、Codex、Pi、Antigravity 等宿主環境 |
| worktree | Git worktree | 保留 Git 術語 |
| TDD | TDD | 首次可補「測試驅動開發」 |
| RED-GREEN-REFACTOR | RED-GREEN-REFACTOR | 不翻譯三階段名稱 |
| YAGNI | YAGNI | 首次可解釋「現在用不到就不要做」 |
| DRY | DRY | 首次可解釋「避免重複」 |
| spec | 規格（spec） | 依上下文也可寫「設計規格」 |
| implementation plan | 實作計畫（implementation plan） | 不譯為「執行方案」以免混淆 executing-plans |
| code review | code review | 首次可補「程式碼審查」 |
| upstream | 上游（upstream） | 指原始 Superpowers repository |
| baseline | 基準（baseline） | 常用於測試初始狀態 |
| refactor | 重構（refactor） | 首次保留英文 |
| checkpoint | 檢查點（checkpoint） | 人工確認或批次中斷點 |
| commit | commit | 不譯為「提交」以保持 Git 語境 |
| PR | PR | 必要時寫 pull request |

## 固定譯法

| 英文 | 固定譯法 | 備註 |
|---|---|---|
| workflow | 工作流程 | 不是「工作流」也可接受，但本專案統一用「工作流程」 |
| process | 流程 | 依上下文可寫「程序」 |
| codebase | 程式碼庫 | |
| requirement | 需求 | |
| constraint | 約束 | 不譯為「限制條件」除非句子較自然 |
| success criteria | 成功判準 | |
| trade-off | 取捨 | |
| design | 設計 | |
| design approval | 設計批准 | |
| implementation | 實作 | |
| verification | 驗證 | |
| validation | 確認／驗證 | 依上下文選擇；規格確認偏「確認」 |
| evidence | 證據 | |
| root cause | 根因 | |
| root-cause analysis | 根因分析 | |
| systematic debugging | 系統化除錯 | |
| ad hoc | 臨時拼湊／隨機應對 | 依語境選擇 |
| clarification | 釐清 | |
| clarifying question | 釐清問題 | |
| user intent | 使用者意圖 | |
| pressure scenario | 壓力情境 | 用於 skill 行為測試 |
| baseline behavior | 基準行為 | |
| rationalization | 合理化藉口 | 指 agent 為跳過規則找理由 |
| loophole | 漏洞／可鑽的空子 | 依句子選擇 |
| compliance | 遵循程度／規格遵循 | |
| anti-pattern | 反模式 | |
| red flag | 警訊 | |
| task | 任務 | |
| task list | 任務清單 | |
| batch | 批次 | |
| dispatch | 派發 | 多用於派發 subagent |
| parallel | 平行 | 台灣用語，不用「並行」作預設 |
| isolated workspace | 隔離工作空間 | |
| clean test baseline | 乾淨的測試基準 | |
| self-review | 自我審查 | |
| user review gate | 使用者審查關卡 | |
| terminal state | 終止狀態 | 指流程結束時必須進入的下一步 |
| visual companion | 視覺輔助工具（Visual Companion） | 首次保留產品名稱 |
| behavior equivalence | 行為等價 | 本地化核心目標 |
| discovery | 發現／搜尋 | skill discovery 統一寫「skill 搜尋與載入」較清楚 |
| trigger | 觸發 | |

## 強制詞

| 原文 | 固定譯法 |
|---|---|
| MUST | 必須（MUST） |
| MUST NOT | 禁止（MUST NOT） |
| Do NOT | 不得（Do NOT） |
| REQUIRED | 必要（REQUIRED） |
| NEVER | 絕對不要（NEVER） |
| ONLY | 只能（ONLY） |
| should | 應 |
| prefer | 優先 |
| may / can | 可以 |

## 容易混淆的詞

### plan

- `writing-plans`：撰寫實作計畫
- `executing-plans`：執行既有實作計畫
- plan mode：規劃模式
- design：設計，不要一律翻成 plan

### review

- code review：code review／程式碼審查
- spec review：規格審查
- self-review：自我審查
- user review：使用者審查

### check

依上下文選擇：

- 檢查：一般確認動作
- 確認：確認狀態或事實
- 驗證：需要證據、測試或可重現結果

### apply / applicable

- skill applies：skill 適用
- apply a change：套用變更
- apply a rule：套用規則

## 新增術語流程

新增固定譯法時：

1. 確認 repo 內是否已有既定用法
2. 確認該詞是一般中文還是框架識別字
3. 優先選擇台灣工程團隊常用說法
4. 在 PR 中說明為何新增或更改
5. 若改動既有術語，搜尋並同步所有已翻譯 skill
