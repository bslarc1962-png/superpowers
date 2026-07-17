---
name: using-git-worktrees
description: "Use when starting feature work that needs isolation from current workspace or before executing implementation plans - ensures an isolated workspace exists via native tools or git worktree fallback. 適用於開始需要與目前工作區隔離的功能開發、或執行實作計畫之前。"
---

# 使用 Git Worktrees

## 概觀

確保工作在隔離的工作區中進行。優先使用你所在平台的原生 worktree 工具。只有在沒有原生工具可用時，才退回手動的 Git worktree。

**核心原則：**先偵測是否已存在隔離環境。接著使用原生工具。再退回 Git。絕對不要（NEVER）與 harness 對抗。

**開始時宣告：**「我正在使用 using-git-worktrees skill 來建立隔離的工作區。」

## Step 0：偵測既有的隔離環境

**在建立任何東西之前，先確認你是否已經在一個隔離的工作區中。**

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

**Submodule 防呆：**`GIT_DIR != GIT_COMMON` 在 git submodule 內部同樣成立。在斷定「已經在 worktree 中」之前，先確認你不在 submodule 內：

```bash
# 如果這個指令回傳一個路徑，代表你在 submodule 中，而不是 worktree —— 當成一般 repo 處理
git rev-parse --show-superproject-working-tree 2>/dev/null
```

**若 `GIT_DIR != GIT_COMMON`（且不是 submodule）：**你已經在一個 linked worktree 中。直接跳到 Step 2（Project Setup）。不得（Do NOT）再建立另一個 worktree。

回報時附上 branch 狀態：
- 在某個 branch 上：「已在隔離工作區 `<path>`，branch 為 `<name>`。」
- Detached HEAD：「已在隔離工作區 `<path>`（detached HEAD，由外部管理）。完成時需要建立 branch。」

**若 `GIT_DIR == GIT_COMMON`（或在 submodule 中）：**你在一般的 repo checkout 中。

使用者是否已在給你的指示中表明 worktree 偏好？如果沒有，在建立 worktree 前先徵求同意：

> 「要我幫你建立一個隔離的 worktree 嗎？它能保護你目前的 branch 不被改動。」

如果已有明確宣告的偏好，直接依循、不必再問。如果使用者拒絕，就地工作並跳到 Step 2。

## Step 1：建立隔離工作區

**你有兩種機制。依這個順序嘗試。**

### 1a. 原生 Worktree 工具（優先）

使用者已要求隔離工作區（Step 0 的同意）。你是否已經有建立 worktree 的方法？它可能是名稱類似 `EnterWorktree`、`WorktreeCreate` 的工具、`/worktree` 命令，或 `--worktree` 旗標。如果有，就使用它並跳到 Step 2。

原生工具會自動處理目錄放置、branch 建立與清理。當你已有原生工具卻仍使用 `git worktree add`，會產生 harness 看不見也管不到的幽靈狀態。

只有在沒有任何原生 worktree 工具可用時，才進入 Step 1b。

### 1b. Git Worktree 後備方案

**只有在 Step 1a 不適用時才使用這個方案**——也就是你沒有任何原生 worktree 工具可用。以 git 手動建立 worktree。

#### 目錄選擇

依下列優先順序。明確的使用者偏好永遠勝過觀察到的檔案系統狀態。

1. **檢查你的指示中是否有宣告的 worktree 目錄偏好。**如果使用者已指定，直接使用、不必再問。

2. **檢查是否已有 project-local 的 worktree 目錄：**
   ```bash
   ls -d .worktrees 2>/dev/null     # 優先（隱藏目錄）
   ls -d worktrees 2>/dev/null      # 替代選項
   ```
   如果找到，就使用它。如果兩者都存在，`.worktrees` 優先。

3. **如果沒有其他可依循的指引**，預設使用專案根目錄下的 `.worktrees/`。

#### 安全驗證（僅限 project-local 目錄）

**在建立 worktree 前必須（MUST）確認目錄已被忽略：**

```bash
git check-ignore -q .worktrees 2>/dev/null || git check-ignore -q worktrees 2>/dev/null
```

**若尚未被忽略：**加入 .gitignore、commit 這項變更，再繼續。

**為何關鍵：**避免不小心把 worktree 內容 commit 進 repository。

#### 建立 Worktree

```bash
# 依所選位置決定路徑
path="$LOCATION/$BRANCH_NAME"

git worktree add "$path" -b "$BRANCH_NAME"
cd "$path"
```

**Sandbox 後備：**如果 `git worktree add` 因權限錯誤（sandbox 拒絕）而失敗，告知使用者 sandbox 阻擋了 worktree 建立，你將改在目前目錄中工作。接著就地執行 setup 與 baseline 測試。

## Step 2：Project Setup

自動偵測並執行適當的 setup：

```bash
# Node.js
if [ -f package.json ]; then npm install; fi

# Rust
if [ -f Cargo.toml ]; then cargo build; fi

# Python
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
if [ -f pyproject.toml ]; then poetry install; fi

# Go
if [ -f go.mod ]; then go mod download; fi
```

## Step 3：驗證乾淨的 Baseline

執行測試以確認工作區從乾淨狀態開始：

```bash
# 使用專案適用的命令
npm test / cargo test / pytest / go test ./...
```

**若測試失敗：**回報失敗，詢問要繼續還是先調查。

**若測試通過：**回報就緒。

### 回報

```
Worktree ready at <full-path>
Tests passing (<N> tests, 0 failures)
Ready to implement <feature-name>
```

## 快速參考

| 情況 | 動作 |
|-----------|--------|
| 已在 linked worktree 中 | 跳過建立（Step 0） |
| 在 submodule 中 | 當成一般 repo 處理（Step 0 防呆） |
| 有原生 worktree 工具 | 使用它（Step 1a） |
| 沒有原生工具 | Git worktree 後備方案（Step 1b） |
| `.worktrees/` 存在 | 使用它（確認已被忽略） |
| `worktrees/` 存在 | 使用它（確認已被忽略） |
| 兩者都存在 | 使用 `.worktrees/` |
| 兩者都不存在 | 檢查指示檔，再預設 `.worktrees/` |
| 目錄未被忽略 | 加入 .gitignore ＋ commit |
| 建立時發生權限錯誤 | Sandbox 後備，就地工作 |
| Baseline 期間測試失敗 | 回報失敗 ＋ 詢問 |
| 沒有 package.json／Cargo.toml | 跳過相依套件安裝 |

## 常見錯誤

### 與 harness 對抗

- **問題：**平台已提供隔離時，仍使用 `git worktree add`
- **修正：**Step 0 會偵測既有隔離環境。Step 1a 會交由原生工具處理。

### 略過偵測

- **問題：**在既有 worktree 內部建立巢狀 worktree
- **修正：**建立任何東西前，永遠先執行 Step 0

### 略過忽略驗證

- **問題：**worktree 內容被追蹤，污染 git status
- **修正：**建立 project-local worktree 前，永遠先用 `git check-ignore`

### 擅自假設目錄位置

- **問題：**造成不一致，違反專案慣例
- **修正：**依優先順序：明確指示 ＞ 既有 project-local 目錄 ＞ 預設

### 帶著失敗的測試繼續進行

- **問題：**無法分辨新 bug 與既有問題
- **修正：**回報失敗，取得明確許可再繼續

## 警訊（Red Flags）

**絕對不要（Never）：**
- 在 Step 0 偵測到既有隔離環境時，還建立 worktree
- 在你已有原生 worktree 工具（例如 `EnterWorktree`）時，使用 `git worktree add`。這是第一大錯誤——有就用它。
- 直接跳到 Step 1b 的 git 命令而略過 Step 1a
- 未驗證目錄已被忽略（project-local）就建立 worktree
- 略過 baseline 測試驗證
- 未詢問就帶著失敗的測試繼續進行

**永遠（Always）：**
- 先執行 Step 0 偵測
- 優先使用原生工具，而非 git 後備方案
- 遵循目錄優先順序：明確指示 ＞ 既有 project-local 目錄 ＞ 預設
- project-local 情況下，確認目錄已被忽略
- 自動偵測並執行 project setup
- 驗證乾淨的測試 baseline
