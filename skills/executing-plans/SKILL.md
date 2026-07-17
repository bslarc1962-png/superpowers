---
name: executing-plans
description: "Use when you have a written implementation plan to execute in a separate session with review checkpoints. 適用於已有寫好的實作計畫、要在獨立 session 中搭配審查 checkpoint 執行時。"
---

# 執行計畫

## 概觀

載入計畫、批判性審查、執行所有 task,完成後回報。

**開始時宣告：**「我正在使用 executing-plans skill 來實作這份計畫。」

**注意：**告訴你合作的人類使用者,Superpowers 在能使用 subagent 的環境下運作得好很多。如果在支援 subagent 的平台上執行,產出品質會明顯更高（Claude Code、Codex CLI、Codex App 與 Copilot CLI 都符合;各平台的工具說明見 `../using-superpowers/references/`）。如果有 subagent 可用,改用 superpowers:subagent-driven-development,而不是這個 skill。

## 流程

### Step 1：載入並審查計畫
1. 讀取計畫檔
2. 批判性審查——找出對計畫的任何疑問或疑慮
3. 若有疑慮：在開始前先向你合作的人類使用者提出
4. 若無疑慮：為計畫項目建立 todo 並繼續

### Step 2：執行 Task

對每個 task：
1. 標記為 in_progress
2. 完整遵循每個步驟（計畫已拆成一口大小的步驟）
3. 依指定執行各項 verification
4. 標記為 completed

### Step 3：完成開發

所有 task 完成並驗證後：
- 宣告：「我正在使用 finishing-a-development-branch skill 來完成這項工作。」
- **REQUIRED SUB-SKILL：**使用 superpowers:finishing-a-development-branch
- 依該 skill 驗證測試、提出選項、執行選擇

## 何時停下來求助

**遇到下列情況立即停止執行：**
- 撞到 blocker（缺少相依套件、測試失敗、指示不清）
- 計畫有關鍵缺口,無法開始
- 你看不懂某個指示
- Verification 反覆失敗

**寧可請對方釐清,也不要用猜的。**

## 何時回到先前步驟

**在下列情況回到審查（Step 1）：**
- 合作的人類使用者依你的回饋更新了計畫
- 基本做法需要重新思考

**不要硬闖 blocker**——停下來詢問。

## 謹記
- 先批判性審查計畫
- 完整遵循計畫步驟
- 不要略過 verification
- 計畫要求時,參照對應的 skill
- 卡住就停,不要用猜的
- 未取得使用者明確同意前,絕對不要（Never）在 main／master branch 上開始實作

## 整合

**必要的工作流程 skill：**
- **superpowers:using-git-worktrees**——確保有隔離的工作區（建立新的或確認既有的）
- **superpowers:writing-plans**——建立本 skill 要執行的計畫
- **superpowers:finishing-a-development-branch**——所有 task 完成後結束開發
