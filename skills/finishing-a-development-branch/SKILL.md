---
name: finishing-a-development-branch
description: "Use when implementation is complete, all tests pass, and you need to decide how to integrate the work - guides completion of development work by presenting structured options for merge, PR, or cleanup. 適用於實作完成、所有測試通過、需要決定如何整合成果時。"
---

# 結束開發分支

## 概觀

以清楚的選項引導開發工作收尾，並處理所選的工作流程。

**核心原則：**驗證測試 → 偵測環境 → 提出選項 → 執行選擇 → 清理。

**開始時宣告：**「我正在使用 finishing-a-development-branch skill 來完成這項工作。」

## 流程

### Step 1：驗證測試

**在提出選項之前，先驗證測試通過：**

```bash
# 執行專案的測試套件
npm test / cargo test / pytest / go test ./...
```

**若測試失敗：**
```
測試失敗（<N> 個失敗）。完成前必須修正：

[顯示失敗內容]

在測試通過前,無法進行 merge／PR。
```

停止。不要進入 Step 2。

**若測試通過：**繼續 Step 2。

### Step 2：偵測環境

**在提出選項前，先判斷工作區狀態：**

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
```

這決定要顯示哪個選單，以及清理方式：

| 狀態 | 選單 | 清理 |
|-------|------|------|
| `GIT_DIR == GIT_COMMON`（一般 repo） | 標準 4 選項 | 沒有 worktree 需要清理 |
| `GIT_DIR != GIT_COMMON`、具名 branch | 標準 4 選項 | 依來源判定（見 Step 6） |
| `GIT_DIR != GIT_COMMON`、detached HEAD | 縮減為 3 選項（無 merge） | 不清理（由外部管理） |

### Step 3：判定 base branch

```bash
# 嘗試常見的 base branch
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

或詢問：「這個 branch 是從 main 分出來的——對嗎？」

### Step 4：提出選項

**一般 repo 與具名 branch worktree——完全照這 4 個選項提出：**

```
實作完成。你想怎麼做？

1. 在本機 merge 回 <base-branch>
2. Push 並建立 Pull Request
3. 保留 branch 現狀（我之後自己處理）
4. 捨棄這份工作

選哪一個？
```

**Detached HEAD——完全照這 3 個選項提出：**

```
實作完成。你目前在 detached HEAD（由外部管理的工作區）。

1. Push 成新 branch 並建立 Pull Request
2. 保留現狀（我之後自己處理）
3. 捨棄這份工作

選哪一個？
```

**不要加說明**——選項保持精簡。

### Step 5：執行選擇

#### 選項 1：在本機 Merge

```bash
# 取得主 repo 根目錄以確保 CWD 安全
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"

# 先 merge——確認成功後才移除任何東西
git checkout <base-branch>
git pull
git merge <feature-branch>

# 在 merge 後的結果上驗證測試
<test command>

# 只有在 merge 成功後：清理 worktree（Step 6）,再刪除 branch
```

接著：清理 worktree（Step 6），再刪除 branch：

```bash
git branch -d <feature-branch>
```

#### 選項 2：Push 並建立 PR

```bash
# Push branch
git push -u origin <feature-branch>
```

**不得（Do NOT）清理 worktree**——使用者需要它保持存在，以便針對 PR 回饋迭代。

#### 選項 3：保留現狀

回報：「保留 branch <name>。Worktree 保留於 <path>。」

**不要清理 worktree。**

#### 選項 4：捨棄

**先確認：**
```
這將永久刪除：
- Branch <name>
- 所有 commit：<commit-list>
- 位於 <path> 的 worktree

輸入 'discard' 以確認。
```

等待完全一致的確認字串。

若已確認：
```bash
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"
```

接著：清理 worktree（Step 6），再強制刪除 branch：
```bash
git branch -D <feature-branch>
```

### Step 6：清理工作區

**只在選項 1 與 4 執行。** 選項 2 與 3 一律保留 worktree。

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
WORKTREE_PATH=$(git rev-parse --show-toplevel)
```

**若 `GIT_DIR == GIT_COMMON`：**一般 repo，沒有 worktree 需要清理。完成。

**若 worktree 路徑位於 `.worktrees/` 或 `worktrees/` 之下：**這個 worktree 是 Superpowers 建立的——清理由我們負責。

```bash
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"
git worktree remove "$WORKTREE_PATH"
git worktree prune  # 自我修復：清掉任何殘留的註冊
```

**否則：**這個工作區由宿主環境（harness）擁有。不得（Do NOT）移除它。如果你的平台提供 workspace-exit 工具，使用它。否則，讓工作區留在原處。

## 快速參考

| 選項 | Merge | Push | 保留 Worktree | 清理 Branch |
|--------|-------|------|---------------|----------------|
| 1. 本機 merge | 是 | - | - | 是 |
| 2. 建立 PR | - | 是 | 是 | - |
| 3. 保留現狀 | - | - | 是 | - |
| 4. 捨棄 | - | - | - | 是（強制） |

## 常見錯誤

**略過測試驗證**
- **問題：**merge 了壞掉的程式碼，建立會失敗的 PR
- **修正：**永遠先驗證測試，再提出選項

**開放式問題**
- **問題：**「接下來我該做什麼？」語意含糊
- **修正：**完全提出 4 個結構化選項（detached HEAD 為 3 個）

**替選項 2 清理 worktree**
- **問題：**移除了使用者要用來迭代 PR 的 worktree
- **修正：**只在選項 1 與 4 清理

**在移除 worktree 前就刪除 branch**
- **問題：**`git branch -d` 失敗，因為 worktree 仍引用該 branch
- **修正：**先 merge，移除 worktree，再刪除 branch

**在 worktree 內部執行 git worktree remove**
- **問題：**當 CWD 位於正要移除的 worktree 內，命令會無聲失敗
- **修正：**執行 `git worktree remove` 前，永遠先 `cd` 到主 repo 根目錄

**清理 harness 擁有的 worktree**
- **問題：**移除 harness 建立的 worktree 會造成幽靈狀態
- **修正：**只清理位於 `.worktrees/` 或 `worktrees/` 之下的 worktree

**捨棄前未確認**
- **問題：**不小心刪掉工作成果
- **修正：**要求輸入 "discard" 確認

## 警訊（Red Flags）

**絕對不要（Never）：**
- 帶著失敗的測試繼續
- 未在結果上驗證測試就 merge
- 未確認就刪除工作成果
- 未經明確要求就 force-push
- 在確認 merge 成功前就移除 worktree
- 清理不是你建立的 worktree（來源檢查）
- 在 worktree 內部執行 `git worktree remove`

**永遠（Always）：**
- 提出選項前先驗證測試
- 提出選單前先偵測環境
- 完全提出 4 個選項（detached HEAD 為 3 個）
- 選項 4 需取得輸入的確認字串
- 只在選項 1 與 4 清理 worktree
- 移除 worktree 前先 `cd` 到主 repo 根目錄
- 移除後執行 `git worktree prune`
