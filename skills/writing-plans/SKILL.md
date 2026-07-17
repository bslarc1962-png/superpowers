---
name: writing-plans
description: "Use when you have a spec or requirements for a multi-step task, before touching code. 適用於已有多步驟任務的規格或需求、開始動手寫程式碼之前。"
---

# 撰寫實作計畫

## 概觀

撰寫完整的實作計畫（implementation plan），假設接手的工程師對我們的程式碼庫毫無背景，品味也不一定可靠。把他們需要知道的一切都寫清楚：每個 task 要動哪些檔案、程式碼、測試、可能要查閱的文件，以及如何測試。把整份計畫拆成一口大小的 task 交給他們。DRY、YAGNI、TDD、頻繁 commit。

假設對方是熟練的開發者，但對我們的工具鏈與問題領域幾乎一無所知。假設他們不太擅長良好的測試設計。

**開始時宣告：**「我正在使用 writing-plans skill 來建立實作計畫。」

**背景：**如果在隔離的 Git worktree 中工作，該 worktree 應已在執行階段透過 `superpowers:using-git-worktrees` skill 建立。

**計畫儲存位置：**`docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`
- （使用者對計畫位置的偏好優先於此預設值）

## 範圍檢查

如果規格涵蓋多個彼此獨立的子系統，它應該在 brainstorming 階段就被拆成各個子專案規格。如果沒有，建議把它拆成多份計畫——每個子系統一份。每份計畫都應能各自產出可運作、可測試的軟體。

## 檔案結構

在定義 task 之前，先規劃哪些檔案會被建立或修改，以及每個檔案負責什麼。分解決策就是在這裡定案的。

- 設計出邊界清楚、介面定義明確的單元。每個檔案只有一個明確責任。
- 你對能一次完整放進 context 的程式碼推理得最好，而且當檔案聚焦時，你的修改也更可靠。優先使用較小、聚焦的檔案，而不是包山包海的大檔案。
- 會一起改動的檔案應放在一起。依責任拆分，而不是依技術分層拆分。
- 在既有程式碼庫中，遵循已建立的模式。如果程式碼庫使用大檔案，不要擅自重構——但如果你正在修改的檔案已經變得難以維護，把拆分納入計畫是合理的。

這個結構會影響 task 的分解。每個 task 都應產出可獨立成立的變更。

## Task 大小拿捏

一個 task 是能自帶測試週期、且值得一位全新 reviewer 把關的最小單位。畫定 task 邊界時：把 setup、設定、鷹架與文件步驟折進真正需要它們的那個 task；只有在 reviewer 可能會有意義地拒絕其中一個 task、卻批准其相鄰 task 時，才拆開。每個 task 都以一個可獨立測試的交付物作結。

## 一口大小的 Task 顆粒度

**每個步驟是一個動作（2–5 分鐘）：**
- 「撰寫會失敗的測試」——一個步驟
- 「執行它以確認它會失敗」——一個步驟
- 「撰寫讓測試通過的最小程式碼」——一個步驟
- 「執行測試並確認它們通過」——一個步驟
- 「Commit」——一個步驟

## 計畫文件標頭

**每份計畫都必須（MUST）以下列標頭開頭：**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

## Global Constraints

[The spec's project-wide requirements — version floors, dependency limits,
naming and copy rules, platform requirements — one line each, with exact
values copied verbatim from the spec. Every task's requirements implicitly
include this section.]

---
```

## Task 結構

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Interfaces:**
- Consumes: [what this task uses from earlier tasks — exact signatures]
- Produces: [what later tasks rely on — exact function names, parameter
  and return types. A task's implementer sees only their own task; this
  block is how they learn the names and types neighboring tasks use.]

- [ ] **Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

- [ ] **Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## 不得留下 Placeholder

每個步驟都必須包含工程師實際需要的內容。下列都是**計畫失誤**——絕對不要（NEVER）這樣寫：
- 「TBD」、「TODO」、「implement later」、「fill in details」
- 「Add appropriate error handling」／「add validation」／「handle edge cases」
- 「Write tests for the above」（沒有實際測試程式碼）
- 「Similar to Task N」（把程式碼重寫一遍——工程師可能不按順序閱讀 task）
- 只描述要做什麼、卻沒有示範怎麼做的步驟（涉及程式碼的步驟必須附程式碼區塊）
- 引用了任何 task 都未定義的型別、function 或 method

## 謹記
- 永遠寫出精確的檔案路徑
- 每個步驟都要有完整程式碼——如果步驟會改動程式碼，就把程式碼寫出來
- 精確的命令與預期輸出
- DRY、YAGNI、TDD、頻繁 commit

## 自我審查

寫完整份計畫後，用全新的角度重看規格，並拿計畫和它對照。這是一份你自己執行的 checklist——不是派發 subagent。

**1. 規格覆蓋度：**逐一略讀規格中的每個章節／需求。你能指出哪個 task 實作了它嗎？列出任何缺口。

**2. Placeholder 掃描：**在你的計畫中搜尋警訊——上方「不得留下 Placeholder」章節列出的任何模式。修正它們。

**3. 型別一致性：**你在後面 task 使用的型別、method 簽章與屬性名稱，是否和你在前面 task 定義的一致？在 Task 3 叫 `clearLayers()`、在 Task 7 卻叫 `clearFullLayers()`，就是一個 bug。

如果發現問題，就地修正即可。不需要再審查一輪——修好就往下走。如果發現某個規格需求沒有對應 task，補上該 task。

## 執行交接

儲存計畫後，提供執行方式的選擇：

**「計畫已完成並儲存至 `docs/superpowers/plans/<filename>.md`。有兩種執行選項：**

**1. Subagent 驅動（推薦）**——我為每個 task 派發一個全新的 subagent，在 task 之間審查，迭代快速

**2. Inline 執行**——在本 session 中使用 executing-plans 執行 task，以批次搭配 checkpoint 執行

**要用哪一種？」**

**若選擇 Subagent 驅動：**
- **REQUIRED SUB-SKILL：**使用 superpowers:subagent-driven-development
- 每個 task 一個全新 subagent ＋ 兩階段審查

**若選擇 Inline 執行：**
- **REQUIRED SUB-SKILL：**使用 superpowers:executing-plans
- 以批次搭配 checkpoint 執行，供審查
