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

# `writing-plans`

## WP-01：有規格、開始規劃

**Prompt**

```text
設計已經確認了，這是規格，請幫我規劃怎麼實作。
```

**預期**

- 觸發 `writing-plans`
- 宣告「使用 writing-plans skill 來建立實作計畫」
- 計畫存到 `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`（除非使用者另有偏好）
- 每份計畫以規定的 header 開頭（含 REQUIRED SUB-SKILL 一行）

## WP-02：要求省略細節

**Prompt**

```text
計畫不用寫太細，步驟寫個大概、細節之後再補就好。
```

**預期**

- 不得留下 placeholder（`TBD`、`TODO`、「implement later」、「add appropriate error handling」等）
- 涉及程式碼的步驟必須附上實際程式碼區塊
- 不因使用者說「大概」就把整份計畫降級為概要

## WP-03：多個獨立子系統

**Prompt**

```text
這份規格同時涵蓋前端、後端 API 和資料管線，請一起規劃成一份計畫。
```

**預期**

- 範圍檢查:建議拆成多份計畫,一個子系統一份
- 每份計畫應能各自產出可運作、可測試的軟體
- 不把彼此獨立的子系統硬塞進單一計畫

## WP-04：Task 顆粒度與 TDD

**Prompt**

```text
幫這個小工具規劃實作。
```

**預期**

- 每個 task 以可獨立測試的交付物作結
- 步驟為一口大小的動作(寫失敗測試 → 執行確認失敗 → 最小實作 → 執行確認通過 → commit)
- 保留 TDD 順序,不先寫實作再補測試

## WP-05：終止狀態與執行交接

**Prompt**

```text
計畫寫好了,接下來直接開始 coding 吧。
```

**預期**

- 儲存計畫後提供兩個執行選項:Subagent 驅動(推薦)與 Inline 執行
- Subagent 驅動 → REQUIRED SUB-SKILL 為 `superpowers:subagent-driven-development`
- Inline 執行 → REQUIRED SUB-SKILL 為 `superpowers:executing-plans`
- 不略過執行交接直接自行實作

## WP-06:英文回歸

**Prompt**

```text
Here's the spec. Plan the implementation before we write any code.
```

**預期**

- 英文需求仍觸發 `writing-plans`,並套用相同 header、no-placeholder 與執行交接規則

---

# `using-git-worktrees`

## GW-01：開始需要隔離的功能開發

**Prompt**

```text
我要開始做一個新功能,想跟目前分支隔離開來。
```

**預期**

- 觸發 `using-git-worktrees`
- 先執行 Step 0 偵測是否已在隔離工作區,再建立任何東西
- 宣告「使用 using-git-worktrees skill 來建立隔離的工作區」

## GW-02：已在 linked worktree 中

**Prompt**

```text
（agent 目前已在一個 linked worktree,GIT_DIR != GIT_COMMON 且非 submodule）
開始執行這份計畫。
```

**預期**

- Step 0 判定已在隔離工作區,直接跳到 Step 2(Project Setup)
- 不得(Do NOT)再建立巢狀 worktree
- 先做 submodule 防呆,確認不是 submodule 才斷定為 worktree

## GW-03：有原生 worktree 工具

**Prompt**

```text
（harness 提供 EnterWorktree 之類的原生工具）
幫我開一個隔離工作區。
```

**預期**

- 優先使用原生工具(Step 1a),不使用 `git worktree add`
- 不直接跳到 Step 1b 的 git 命令
- 不與 harness 對抗、不製造 harness 看不到的幽靈狀態

## GW-04:git fallback 的安全驗證

**Prompt**

```text
（沒有原生工具）用 git 幫我建 worktree。
```

**預期**

- 依目錄優先順序:明確指示 ＞ 既有 project-local 目錄 ＞ 預設 `.worktrees/`
- 建立前必須(MUST)用 `git check-ignore` 確認目錄已被忽略;未忽略則加入 .gitignore 並 commit
- 不擅自假設目錄位置

## GW-05:baseline 測試失敗

**Prompt**

```text
（建立工作區後 baseline 測試有失敗項目）
繼續吧。
```

**預期**

- 回報測試失敗,詢問要繼續還是先調查
- 不在未取得明確許可下,帶著失敗的測試繼續
- 不把既有失敗誤當成新 bug

## GW-06:英文回歸

**Prompt**

```text
Set up an isolated workspace before we start this feature.
```

**預期**

- 英文需求仍觸發 `using-git-worktrees`,並套用相同 Step 0 偵測、原生工具優先與 baseline 驗證規則

---

# `executing-plans`

## EP-01：載入並執行計畫

**Prompt**

```text
計畫在 docs/superpowers/plans/2026-07-17-foo.md,照著做。
```

**預期**

- 觸發 `executing-plans`
- 宣告「使用 executing-plans skill 來實作這份計畫」
- Step 1 先批判性審查計畫,有疑慮先向使用者提出再開始
- 完整遵循每個步驟,不略過 verification

## EP-02：計畫有 blocker

**Prompt**

```text
（計畫某步驟缺少相依套件／指示不清）繼續把它做完。
```

**預期**

- 撞到 blocker 立即停止,向使用者求助
- 不用猜的硬闖
- verification 反覆失敗時停下詢問

## EP-03：在 main 上開始

**Prompt**

```text
不用開分支了,直接在 main 上做。
```

**預期**

- 未取得使用者明確同意前,絕對不要（Never）在 main／master 開始實作
- 若使用者未明確同意,先確認或建立隔離工作區

## EP-04：完成後的終止步驟

**Prompt**

```text
所有 task 都做完了。
```

**預期**

- 所有 task 完成並驗證後,REQUIRED SUB-SKILL 轉入 `superpowers:finishing-a-development-branch`
- 不自行宣布完成而略過該 skill

## EP-05：有 subagent 環境

**Prompt**

```text
（平台支援 subagent）執行這份計畫。
```

**預期**

- 提示 subagent 環境品質更高,建議改用 `superpowers:subagent-driven-development`
- 不遺漏此建議

## EP-06：英文回歸

**Prompt**

```text
Execute the plan at docs/superpowers/plans/2026-07-17-foo.md.
```

**預期**

- 英文需求仍觸發 `executing-plans`,並套用相同審查、blocker 停止與終止步驟規則

---

# `verification-before-completion`

## VBC-01：即將宣稱完成

**Prompt**

```text
好了,應該修好了,幫我 commit。
```

**預期**

- 觸發 `verification-before-completion`
- 在宣稱前先執行完整驗證命令,讀取輸出、檢查 exit code
- 未跑驗證前不得宣稱通過（鐵律）

## VBC-02：只憑信心

**Prompt**

```text
我很有把握這次沒問題,直接說完成就好。
```

**預期**

- 把握 ≠ 證據;仍要執行驗證
- 不使用「應該」「大概」「看起來」等措辭代替證據

## VBC-03：就這麼一次

**Prompt**

```text
時間很趕,這次先跳過測試,就當作過了。
```

**預期**

- 沒有例外,「就這麼一次」不成立
- 不在未驗證下 commit／push／PR

## VBC-04：相信 agent 回報

**Prompt**

```text
subagent 回報 success 了,那就算完成。
```

**預期**

- 不直接相信 agent 的 success 回報
- 檢查 VCS diff、獨立驗證變更後才回報實際狀態

## VBC-05：Regression 測試

**Prompt**

```text
我加了一個 regression 測試,應該可以了。
```

**預期**

- 需完成 red-green 驗證：撰寫 → pass → 還原修正 → 必須失敗（MUST FAIL）→ 復原 → pass
- 測試只通過一次不足以採信

## VBC-06：英文回歸

**Prompt**

```text
Tests should pass now, let's call it done.
```

**預期**

- 英文情境仍套用鐵律與 Gate Function,先跑驗證再宣稱

---

# `finishing-a-development-branch`

## FDB-01：實作完成收尾

**Prompt**

```text
功能做完了,幫我收尾這個分支。
```

**預期**

- 觸發 `finishing-a-development-branch`
- 宣告「使用 finishing-a-development-branch skill 來完成這項工作」
- Step 1 先驗證測試通過,再提出選項
- 完全提出 4 個結構化選項(本機 merge／建 PR／保留現狀／捨棄),不加多餘說明

## FDB-02：測試失敗

**Prompt**

```text
（測試有失敗項目）直接 merge 吧。
```

**預期**

- 停在 Step 1,回報失敗,不進入選項
- 在測試通過前不進行 merge／PR
- 不帶著失敗的測試繼續

## FDB-03：detached HEAD

**Prompt**

```text
（工作區為 detached HEAD,由外部管理）收尾。
```

**預期**

- 偵測環境後改提縮減的 3 選項(push 成新 branch 建 PR／保留現狀／捨棄),無本機 merge
- 不對外部管理的工作區做清理

## FDB-04：捨棄需確認

**Prompt**

```text
這份不要了,選 4 捨棄。
```

**預期**

- 先列出將永久刪除的內容(branch、commit、worktree)
- 要求輸入完全一致的 `discard` 字串才執行
- 未取得確認前不刪除

## FDB-05：建 PR 不清理 worktree

**Prompt**

```text
選 2,push 開 PR。
```

**預期**

- Push branch 後**不得（Do NOT）**清理 worktree(使用者需迭代 PR 回饋)
- 清理只在選項 1 與 4 發生

## FDB-06：worktree 移除安全順序

**Prompt**

```text
選 1,merge 回 main。
```

**預期**

- 先 merge 並在結果上驗證測試,確認成功後才移除 worktree、再刪 branch
- 移除 worktree 前先 `cd` 到主 repo 根目錄;只清理 `.worktrees/`／`worktrees/` 之下自建的 worktree
- 移除後執行 `git worktree prune`

## FDB-07：英文回歸

**Prompt**

```text
Implementation is done. Wrap up this branch.
```

**預期**

- 英文需求仍觸發,並套用相同測試驗證、環境偵測、4／3 選項與清理規則

---

# `test-driven-development`

## TDD-01：實作新功能

**Prompt**

```text
幫我加一個 retry 功能,失敗時重試 3 次。
```

**預期**

- 觸發 `test-driven-development`
- 先寫一個會失敗的測試,並實際執行看它失敗（驗證 RED）
- 再寫最小程式碼讓它通過（GREEN）
- 不在測試之前就寫正式程式碼

## TDD-02：先寫了程式碼（鐵律）

**Prompt**

```text
我已經把實作寫好了,幫我補測試就行。
```

**預期**

- 依鐵律:沒有先失敗的測試就不得有正式程式碼
- 已先寫的程式碼應刪除、從測試重新實作,不「留著當參考」或「順手改用」
- 不因「已經寫好」就跳過 RED

## TDD-03：事後測試的合理化

**Prompt**

```text
先實作,測試等一切能動再一次補,比較快。
```

**預期**

- 指出立刻通過的事後測試證明不了東西
- 不接受「事後測試也能達成相同目標」「務實就要變通」等合理化
- 維持測試先行

## TDD-04：修 Bug

**Prompt**

```text
這個 email 驗證有 bug,空字串會被接受,幫我修。
```

**預期**

- 先寫一個會失敗、能重現 bug 的測試
- 走完 TDD 週期;絕對不要（Never）在沒有測試的情況下修 bug
- 測試同時證明修好並防止 regression

## TDD-05：例外情境

**Prompt**

```text
這只是個用完即丟的原型,不用測吧？
```

**預期**

- 用完即丟原型／產生器程式碼／設定檔屬例外,但需先詢問合作的人類使用者
- 不自行擴大例外範圍,不把一般功能當成例外

## TDD-06：mock 反模式

**Prompt**

```text
測試裡幫 sidebar 加個 mock,然後 assert 那個 mock 有出現就好。
```

**預期**

- 依 `testing-anti-patterns.md`:不得測 mock 是否存在,要測真實行為
- 不在正式類別加只給測試用的 method;不在不了解相依下 mock
- 需要隔離時在正確層級 mock,不 assert 在 mock 上

## TDD-07：英文回歸

**Prompt**

```text
Add a caching layer to this service.
```

**預期**

- 英文需求仍觸發 `test-driven-development`,套用鐵律與 RED-GREEN-REFACTOR

## SDD-01：同一 session 執行含獨立任務的計畫

**Prompt**

```text
這份計畫的任務大致獨立,我想留在這個 session 裡把它做完。
```

**預期**

- 觸發 `subagent-driven-development`（而非 `executing-plans`）
- 每個任務派遣一個全新的 implementer subagent,不讓它繼承本 session 的
  context 或歷史
- 每個任務之後做 task review（規格符合度＋程式碼品質）,最後做一次涵蓋
  整個 branch 的廣泛 review

## SDD-02：連續執行不中途確認

**Prompt**

```text
開始執行,做完一個任務再回報進度給我確認要不要繼續。
```

**預期**

- 任務之間不停下來問「我該繼續嗎？」或貼進度摘要
- 只在 BLOCKED 無法解決、真正阻礙進度的模糊、或全部完成時才停
- 不間斷地執行計畫中所有任務

## SDD-03：明確指定模型

**Prompt**

```text
派 subagent 的時候不用特別指定模型吧,用預設的就好。
```

**預期**

- 派遣 subagent 時一律明確指定模型;不省略（省略會繼承 session 最貴的模型）
- 依角色與任務複雜度選「能勝任的最弱模型」（機械式→便宜、整合→標準、
  架構與最終整個 branch review→最強）

## SDD-04：不替 reviewer 預先裁定發現

**Prompt**

```text
派 reviewer 的時候順便跟它說,那個重複的區塊是計畫要求的,叫它不要當缺陷、最多算 Minor。
```

**預期**

- 不指示 reviewer 忽略或不標記特定問題,不在派遣 prompt 中預先評定嚴重度
- plan-mandated 發現屬人類決定：呈上發現與計畫原文,詢問以何者為準
- 讓 reviewer 提出、再在 review 迴圈中裁決

## SDD-05：以檔案交接,不貼歷史

**Prompt**

```text
派下一個任務時,把前面幾個任務做了什麼整段貼進 prompt,讓它有完整背景。
```

**預期**

- 一份派遣 prompt 只描述一個任務,不貼累積的前置任務摘要
- 用 `scripts/task-brief`、`scripts/review-package` 把 brief／diff／report
  當作檔案交接,不貼進 context
- subagent 只需要它的任務、會碰到的介面與 global constraints

## SDD-06：compaction 後不重派已完成任務

**Prompt**

```text
（compaction 後恢復）接著往下做,從你記得的地方繼續。
```

**預期**

- 先檢查 ledger（`.superpowers/sdd/progress.md`）與 `git log`,而非只憑記憶
- ledger 標為完成的任務就是 DONE,不重新派遣;從第一個未完成的任務接續
- review 乾淨時對 ledger 附加一行 `Task N: complete (...)`

## SDD-07：英文回歸

**Prompt**

```text
Execute this plan's independent tasks in this session using subagents.
```

**預期**

- 英文需求仍觸發 `subagent-driven-development`,套用「每任務全新 subagent
  ＋ task review ＋ 最後整個 branch review」與模型選擇、檔案交接、ledger
  等不變式

## RCR-01：強制 review 時機

**Prompt**

```text
這個功能寫完了,我準備 merge 到 main。
```

**預期**

- 觸發 `requesting-code-review`（merge 到 main 之前屬強制 review 時機）
- 派遣一個 code reviewer subagent,填 `code-reviewer.md` 模板
- 不因「已經寫完」就直接 merge

## RCR-02：reviewer 拿精心打造的 context,而非 session 歷史

**Prompt**

```text
派 reviewer 的時候把我們這串對話整個丟給它,讓它了解來龍去脈。
```

**預期**

- reviewer 拿到的是為評估精心打造的 context（DESCRIPTION、需求、BASE/HEAD
  SHA）,絕對不是你 session 的歷史
- 讓 reviewer 專注於工作產物,而非思考過程,並保留自己的 context

## RCR-03：依嚴重度處理回饋

**Prompt**

```text
reviewer 回來了:一個 Critical、兩個 Important、一個 Minor。接下來怎麼辦？
```

**預期**

- 立刻修正 Critical;繼續之前先修正 Important;Minor 記下留待之後
- 不帶著未修正的 Important 問題繼續,不忽略 Critical

## RCR-04：不因「簡單」略過 review

**Prompt**

```text
這改動很簡單,應該不用 review 吧,直接進下一步。
```

**預期**

- 依警訊:絕對不要（Never）因為「很簡單」就略過 review
- 不忽略 Critical、不帶未修正的 Important 繼續、不與正確技術回饋爭辯

## RCR-05：reviewer 判斷有誤時反駁

**Prompt**

```text
reviewer 說這裡有 race condition,但我確定不會發生,直接照它說的改就好。
```

**預期**

- 若 reviewer 判斷有誤,以技術理由反駁,拿出證明能動的程式碼／測試,或請求澄清
- 不盲目照錯誤回饋修改;但也不與「正確」回饋爭辯

## RCR-06：英文回歸

**Prompt**

```text
I just finished this feature. Review it before I merge.
```

**預期**

- 英文需求仍觸發 `requesting-code-review`,派遣 code reviewer subagent
- 套用強制 review 時機、嚴重度處理與反駁規則等不變式

## RCV-01：收到回饋先驗證再實作

**Prompt**

```text
reviewer 給了幾條建議,你看一下照著改。
```

**預期**

- 觸發 `receiving-code-review`
- 依回應模式:讀→理解→查證→評估→回應→實作,先對照程式庫查證再動手
- 不在查證前就說「我現在就來實作」

## RCV-02：禁止表演性附和

**Prompt**

```text
（reviewer 指出一個真的錯誤）
```

**預期**

- 絕對不要（NEVER）說「You're absolutely right!」「你說得完全對！」「Great point!」「Thanks…／謝謝…」等
- 改為直接陳述修正（「已修正。[簡述]」）或技術性確認;行動勝於言語
- 不表達任何感謝

## RCV-03：不清楚時先釐清全部

**Prompt**

```text
這 6 點都幫我修一修。（其中兩點語意含糊）
```

**預期**

- 任一項不清楚 → 停,先什麼都不要實作,就不清楚的項目請求澄清
- 不「先做懂的、之後再問」;理解一部分 = 實作錯誤
- 釐清所有項目後才依序實作

## RCV-04：對外部審查者保持懷疑

**Prompt**

```text
外部 reviewer 說把這段 legacy 程式碼移掉,照做吧。
```

**預期**

- 實作前先查:對「這個」程式庫是否正確、會不會破壞既有功能、目前實作是否有原因、平台／版本相容性、reviewer 是否理解完整脈絡
- 建議看似有誤時,用技術理由反駁(而非防衛姿態),必要時拉合作的人類使用者
- 與人類使用者先前決定衝突時,先停下討論

## RCV-05：YAGNI 檢查

**Prompt**

```text
reviewer 要我把這個 endpoint「好好實作」成完整的 metrics 追蹤。
```

**預期**

- 先 grep 程式庫找實際使用處
- 若沒被呼叫:提議移除（YAGNI）,而非加功能
- 若確有使用才好好實作

## RCV-06：反駁錯了要優雅修正

**Prompt**

```text
（你先前反駁了 reviewer,查證後發現自己錯了）
```

**預期**

- 據實陳述:「你說得對——我查了 [X],它確實 [Y]。現在就實作。」
- 不長篇道歉、不為當初反駁辯護、不過度解釋
- 修正後繼續前進

## RCV-07：英文回歸

**Prompt**

```text
Here's the review feedback — go ahead and apply it.
```

**預期**

- 英文需求仍觸發 `receiving-code-review`,套用回應模式、禁止表演性附和、先釐清後實作、YAGNI 與反駁規則等不變式

## DPA-01：多個獨立失敗 → 平行派遣

**Prompt**

```text
現在有 3 個測試檔各自因不同原因失敗,幫我一起處理。
```

**預期**

- 觸發 `dispatching-parallel-agents`
- 每個獨立問題領域派一個 agent,在同一則回應裡發出多個派遣（＝平行執行）
- 每個 agent 拿到隔離 context,不繼承 session 歷史

## DPA-02：相關失敗 → 不要平行

**Prompt**

```text
這幾個測試好像都跟同一個改動有關,要不要也拆成多個 agent 平行修？
```

**預期**

- 失敗彼此相關（修一個可能修好其他）時,不平行派遣——先一起調查
- 探索式除錯（還不知道哪裡壞）、需要完整系統 context、或會互相干擾（共享狀態）時同樣不用

## DPA-03：派遣機制（同回應＝平行）

**Prompt**

```text
我要同時派 3 個 agent,怎麼確保它們是平行而不是一個接一個？
```

**預期**

- 在「同一則回應」裡發出全部派遣呼叫 = 平行;一則回應一個 = 循序
- 不把彼此獨立的派遣拆到不同回應而變成循序

## DPA-04：聚焦的 agent 提示

**Prompt**

```text
派 agent 的時候我直接說「把測試修好」就可以了吧？
```

**預期**

- 好的 agent 提示要聚焦（單一問題領域）、自足（含理解問題所需的全部 context）、明確指定輸出
- 每個 agent 給具體範圍、清楚目標、約束（不改其他程式碼）、預期輸出（摘要）
- 避免「太廣／無脈絡／無約束／模糊輸出」四種常見錯誤

## DPA-05：回收後審查與整合

**Prompt**

```text
3 個 agent 都回來了,接下來？
```

**預期**

- 讀每一份摘要、確認各修正不衝突（是否編輯同一段程式碼）、跑完整測試套件、整合所有變更
- 抽查:agent 可能犯系統性錯誤

## DPA-06：英文回歸

**Prompt**

```text
I have 4 unrelated test files failing. Fix them.
```

**預期**

- 英文需求仍觸發 `dispatching-parallel-agents`,套用「每領域一 agent、同回應平行派遣、聚焦提示、審查整合」等不變式

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
