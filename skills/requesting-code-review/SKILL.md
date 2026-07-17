---
name: requesting-code-review
description: "Use when completing tasks, implementing major features, or before merging to verify work meets requirements. 適用於完成任務、實作重大功能、或 merge 之前,用以驗證成果符合需求。"
---

# 請求程式碼審查（Requesting Code Review）

派遣一個 code reviewer subagent,在問題擴散之前抓到它們。reviewer 拿到的是為評估精心打造的 context——絕對不是你 session 的歷史。這讓 reviewer 專注於工作產物,而非你的思考過程,也保留你自己的 context 供後續工作使用。

**核心原則：**盡早 review、經常 review。

## 何時請求 review

**強制（Mandatory）：**
- subagent-driven development 中每個任務之後
- 完成重大功能之後
- merge 到 main 之前

**非必要但有價值：**
- 卡住時（換個新視角）
- 重構之前（基準檢查）
- 修好複雜 bug 之後

## 如何請求

**1. 取得 git SHA：**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. 派遣 code reviewer subagent：**

派遣一個 `general-purpose` subagent,填入 [code-reviewer.md](code-reviewer.md) 的模板

**佔位符（Placeholders）：**
- `{DESCRIPTION}` - 你所建構內容的簡短摘要
- `{PLAN_OR_REQUIREMENTS}` - 它應該做什麼
- `{BASE_SHA}` - 起始 commit
- `{HEAD_SHA}` - 結束 commit

**3. 依回饋行動：**
- 立刻修正 Critical 問題
- 繼續之前先修正 Important 問題
- 把 Minor 問題記下留待之後
- 若 reviewer 判斷有誤,提出反駁（附理由）

## 範例

```
[剛完成 Task 2：新增驗證 function]

你：繼續之前先請求一次 code review。

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[派遣 code reviewer subagent]
  DESCRIPTION: Added verifyIndex() and repairIndex() with 4 issue types
  PLAN_OR_REQUIREMENTS: Task 2 from docs/superpowers/plans/deployment-plan.md
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661

[Subagent 回傳]：
  Strengths: Clean architecture, real tests
  Issues:
    Important: Missing progress indicators
    Minor: Magic number (100) for reporting interval
  Assessment: Ready to proceed

你：[修正 progress indicators]
[繼續 Task 3]
```

## 與工作流程整合

**Subagent-Driven Development：**
- 每個（EACH）任務之後 review
- 在問題累積之前抓到
- 進到下一個任務之前先修

**Executing Plans：**
- 每個任務之後或在自然的檢查點 review
- 取得回饋、套用、繼續

**臨時性（Ad-Hoc）開發：**
- merge 之前 review
- 卡住時 review

## 警訊（Red Flags）

**絕對不要（Never）：**
- 因為「很簡單」就略過 review
- 忽略 Critical 問題
- 帶著未修正的 Important 問題繼續
- 與正確的技術回饋爭辯

**若 reviewer 判斷有誤：**
- 以技術理由反駁
- 拿出證明它能動的程式碼／測試
- 請求澄清

見模板：[code-reviewer.md](code-reviewer.md)
