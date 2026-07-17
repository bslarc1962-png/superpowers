# Superpowers for OpenCode

在 [OpenCode.ai](https://opencode.ai) 上使用 Superpowers 的完整指南。

## 安裝

把 superpowers 加進你的 `opencode.json`（全域或專案層級）裡的 `plugin` 陣列：

```json
{
  "plugin": ["superpowers@git+https://github.com/obra/superpowers.git"]
}
```

重新啟動 OpenCode。外掛會透過 OpenCode 的外掛管理器安裝，並註冊所有 skill。

驗證方式：問「Tell me about your superpowers」。

OpenCode 使用自己的外掛安裝機制。如果你同時也用 Claude Code、Codex 或其他 harness，請分別為每一個安裝 Superpowers。

### 從舊的 symlink 式安裝遷移

如果你先前是用 `git clone` 加 symlink 安裝 superpowers，請移除舊的設定：

```bash
# Remove old symlinks
rm -f ~/.config/opencode/plugins/superpowers.js
rm -rf ~/.config/opencode/skills/superpowers

# Optionally remove the cloned repo
rm -rf ~/.config/opencode/superpowers

# Remove skills.paths from opencode.json if you added one for superpowers
```

然後依上面的安裝步驟操作。

## 使用方式

### 找出 Skill

用 OpenCode 原生的 `skill` 工具列出所有可用的 skill：

```
use skill tool to list skills
```

### 載入 Skill

```
use skill tool to load brainstorming
```

### 個人 Skill

在 `~/.config/opencode/skills/` 建立你自己的 skill：

```bash
mkdir -p ~/.config/opencode/skills/my-skill
```

建立 `~/.config/opencode/skills/my-skill/SKILL.md`：

```markdown
---
name: my-skill
description: Use when [condition] - [what it does]
---

# My Skill

[Your skill content here]
```

### 專案 Skill

在你專案內的 `.opencode/skills/` 建立專案專屬的 skill。

**Skill 優先順序：** 專案 skill > 個人 skill > Superpowers skill

## 更新

OpenCode 透過 git-backed 的套件規格安裝 Superpowers。某些 OpenCode 與 Bun 版本會把解析出來的 git 相依鎖定在 lockfile 或快取中，因此重新啟動不一定會抓到最新的 Superpowers commit。如果更新沒有出現，請清除 OpenCode 的套件快取，或重新安裝外掛。

若要鎖定特定版本，使用分支或 tag：

```json
{
  "plugin": ["superpowers@git+https://github.com/obra/superpowers.git#v5.0.3"]
}
```

## 運作方式

這個外掛做兩件事：

1. **注入 bootstrap 脈絡**：透過 `experimental.chat.messages.transform` hook，為每一段對話加入對 superpowers 的認知。
2. **註冊 skill 目錄**：透過 `config` hook，讓 OpenCode 不需要 symlink 或手動設定就能發現所有 superpowers skill。

### 工具對應

Skill 用「動作」來描述，而不是指名任何單一執行環境的工具。在 OpenCode 上，這些動作對應到：

- 「Create a todo」／「mark complete in todo list」→ `todowrite`
- `Subagent (general-purpose):` 模板 → OpenCode 的 `task` 工具，搭配 `subagent_type: "general"`（探索程式碼庫時用 `"explore"`）
- 「Invoke a skill」→ OpenCode 原生的 `skill` 工具
- 「Read a file」→ `read`
- 「Create a file」／「edit a file」／「delete a file」→ `apply_patch`
- 「Run a shell command」→ `bash`
- 「Search file contents」／「find files by name」→ `grep`、`glob`
- 「Fetch a URL」→ `webfetch`

（已對照已安裝的 OpenCode CLI 工具清單驗證。）

## 疑難排解

### 外掛沒有載入

1. 檢查 OpenCode 記錄：`opencode run --print-logs "hello" 2>&1 | grep -i superpowers`
2. 確認 `opencode.json` 裡的 plugin 那行正確
3. 確認你執行的是較新版本的 OpenCode

### Windows 安裝問題

某些 Windows 版的 OpenCode 在處理 git-backed 外掛規格時，有上游安裝程式的問題，包括 `git+https` URL 的快取路徑，以及 Bun 找不到 `git.exe`（即使它在一般終端機能正常運作）。如果 OpenCode 無法安裝外掛，試著改用系統的 npm 安裝，再讓 OpenCode 指向本機套件：

```powershell
npm install superpowers@git+https://github.com/obra/superpowers.git --prefix "$HOME\.config\opencode"
```

然後在 `opencode.json` 裡使用已安裝的套件路徑：

```json
{
  "plugin": ["~/.config/opencode/node_modules/superpowers"]
}
```

### 找不到 Skill

1. 用 OpenCode 的 `skill` 工具列出可用的 skill
2. 確認外掛有載入（見上文）
3. 每個 skill 都需要一個帶有效 YAML frontmatter 的 `SKILL.md` 檔案

### Bootstrap 沒有出現

1. 確認 OpenCode 版本支援 `experimental.chat.messages.transform` hook
2. 設定變更後重新啟動 OpenCode

## 取得協助

- 回報問題：https://github.com/obra/superpowers/issues
- 主要文件：https://github.com/obra/superpowers
- OpenCode 文件：https://opencode.ai/docs/
