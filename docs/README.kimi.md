# Superpowers for Kimi Code

在 [Kimi Code](https://github.com/MoonshotAI/kimi-code) 上使用 Superpowers 的完整指南。

## 安裝

Superpowers 已收錄在 Kimi Code 的外掛市集中。

開啟外掛管理器：

```text
/plugins
```

進入 `Marketplace` > `Superpowers` 並安裝。

你也可以直接從這個 repository 安裝：

```text
/plugins install https://github.com/obra/superpowers
```

若要對尚未發布的 `dev` 進行驗證，明確指定該分支：

```text
/plugins install https://github.com/obra/superpowers/tree/dev
```

Kimi Code 會把外掛的變更套用到新的 session。安裝、更新、啟用、停用或重新載入外掛之後，請用 `/new` 開啟一個全新的 session。

## 運作方式

Kimi 外掛的 manifest 位於 `.kimi-plugin/plugin.json`。

這份 manifest 做三件事：

1. 讓 Kimi Code 指向既有的 `skills/` 目錄。
2. 透過 `sessionStart.skill` 在 session 啟動時載入 `using-superpowers`。
3. 透過 `skillInstructions` 提供 Kimi 專屬的工具對應。

Kimi Code 直接從這個 repository 讀取 Superpowers skill。沒有任何複製過去的 skill、symlink、hook 或額外的執行期相依。

## 工具對應

Skill 描述的是「動作」，而不是把某一個執行環境的工具名稱寫死。在 Kimi Code 上，這些動作對應到：

- 「Ask the user」／「ask clarifying questions」-> `AskUserQuestion`
- 「Create a todo」／「mark complete in todo list」-> `TodoList`
- 「Dispatch a subagent」-> `Agent`
- 「Invoke a skill」-> Kimi Code 原生的 `Skill` 工具
- 「Read a file」／「write a file」／「edit a file」-> `Read`、`Write`、`Edit`
- 「Run a shell command」-> `Bash`
- 「Search file contents」-> `Grep`
- 「Find files by path or pattern」-> `Glob`
- 「Fetch a URL」-> `FetchURL`
- 「Search the web」-> `WebSearch`

## 更新

使用 Kimi Code 的外掛管理器：

```text
/plugins
```

選取 Superpowers 並從那裡更新。更新後用 `/new` 開啟一個全新的 session。

## 疑難排解

### 外掛沒有載入

1. 執行 `/plugins info superpowers` 並檢查診斷資訊。
2. 確認外掛已啟用。
3. 安裝或更新後，用 `/new` 開啟一個全新的 session。

### 直接從 GitHub 安裝卻裝到舊的 release

對於裸的 repository URL，只要有 release 存在，Kimi Code 就會安裝最新的 GitHub release。若要在下一個 Superpowers release 之前測試尚未發布的變更，請明確指定分支安裝：

```text
/plugins install https://github.com/obra/superpowers/tree/dev
```

### Skill 沒有觸發

1. 確認 `/plugins info superpowers` 顯示外掛為啟用狀態。
2. 用 `/new` 開啟一個全新的 session。
3. 試試驗收 prompt：`Let's make a react todo list`。正常的安裝應該會在寫程式之前先載入 `brainstorming`。
