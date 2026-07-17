# Superpowers

Superpowers 是一套完整的軟體開發方法論，為你的 coding agent 打造。它建立在一組可組合的 skill 之上，再加上一些初始指示，確保你的 agent 真的會去用這些 skill。


## 我們在徵才!

我們正在徵一位全職夥伴，協助 Superpowers 的社群與程式碼工作。
職缺內容請見 https://primeradiant.com/jobs/superpowers-community-engineer/
如果你想到適合的人選，歡迎把他們介紹給我們。

## 快速開始

給你的 agent 超能力：[Claude Code](#claude-code)、[Antigravity](#antigravity)、[Codex App](#codex-app)、[Codex CLI](#codex-cli)、[Cursor](#cursor)、[Factory Droid](#factory-droid)、[GitHub Copilot CLI](#github-copilot-cli)、[Kimi Code](#kimi-code)、[OpenCode](#opencode)、[Pi](#pi)。

## 運作方式

一切從你啟動 coding agent 的那一刻開始。當它一看到你要建立某個東西，它*不會*直接跳進去寫程式，而是先退一步，問你真正想做的是什麼。

一旦它從對話中理出一份 spec，就會分成短到你真的讀得完、消化得了的段落，一段段拿給你看。

在你確認設計之後，你的 agent 會擬出一份實作計畫，清楚到足以讓「一位熱情、但品味差、沒有判斷力、不懂專案脈絡、又排斥測試的資淺工程師」照著做。計畫強調真正的紅／綠 TDD、YAGNI(You Aren't Gonna Need It)與 DRY。

接著，一旦你說「go」，它就會啟動 *subagent-driven-development* 流程，讓一個個 agent 逐一完成每項工程任務，檢查並審查它們的成果，再繼續往前。你的 agent 連續自主工作好幾個小時、卻始終不偏離你們一起擬定的計畫，並不罕見。

系統當然還有更多細節，但核心就是這樣。而且因為這些 skill 會自動觸發，你不需要做任何特別的事。你的 coding agent 就是有了 Superpowers。

## 商業服務

如果你在企業環境使用 Superpowers，並且需要商業支援、額外工具或受管理的支出方案，歡迎來信 sales@primeradiant.com。

## 安裝

安裝方式因 harness 而異。如果你同時使用多個，請分別為每一個安裝 Superpowers。

### Claude Code

Superpowers 可透過 [Claude 官方外掛市集](https://claude.com/plugins/superpowers)取得。

#### Official Marketplace

- 從 Anthropic 官方市集安裝外掛：

  ```bash
  /plugin install superpowers@claude-plugins-official
  ```

#### Superpowers Marketplace

Superpowers 市集提供 Superpowers 以及其他一些相關的 Claude Code 外掛。

- 註冊市集：

  ```bash
  /plugin marketplace add obra/superpowers-marketplace
  ```

- 從這個市集安裝外掛：

  ```bash
  /plugin install superpowers@superpowers-marketplace
  ```

### Antigravity

從這個 repository 以外掛方式安裝 Superpowers：

```bash
agy plugin install https://github.com/obra/superpowers
```

Antigravity 會執行外掛的 session-start hook，所以 Superpowers 從第一則訊息起就是啟用狀態。用同一道指令重新安裝即可更新。

### Codex App

Superpowers 可透過 [Codex 官方外掛市集](https://github.com/openai/plugins)取得。

- 在 Codex app 中，點選側邊欄的 Plugins。
- 你應該會在 Coding 區看到 `Superpowers`。
- 點選 Superpowers 旁的 `+`，依提示操作。

### Codex CLI

Superpowers 可透過 [Codex 官方外掛市集](https://github.com/openai/plugins)取得。

- 開啟外掛搜尋介面：

  ```bash
  /plugins
  ```

- 搜尋 Superpowers：

  ```bash
  superpowers
  ```

- 選擇 `Install Plugin`。

### Cursor

- 在 Cursor Agent 對話中，從市集安裝：

  ```text
  /add-plugin superpowers
  ```

- 或在外掛市集搜尋「superpowers」。

### Factory Droid

- 註冊市集：

  ```bash
  droid plugin marketplace add https://github.com/obra/superpowers
  ```

- 安裝外掛：

  ```bash
  droid plugin install superpowers@superpowers
  ```

### GitHub Copilot CLI

- 註冊市集：

  ```bash
  copilot plugin marketplace add obra/superpowers-marketplace
  ```

- 安裝外掛：

  ```bash
  copilot plugin install superpowers@superpowers-marketplace
  ```

### Kimi Code

Superpowers 已收錄在 Kimi Code 的外掛市集中。

- 開啟 Kimi Code 的外掛管理器：

  ```text
  /plugins
  ```

- 進入 `Marketplace` > `Superpowers` 並安裝。

- 或直接從這個 repository 安裝：

  ```text
  /plugins install https://github.com/obra/superpowers
  ```

- 詳細文件：[docs/README.kimi.md](docs/README.kimi.md)

### OpenCode

OpenCode 使用自己的外掛安裝機制;即使你已在其他 harness 用過，也要為它單獨安裝 Superpowers。

- 告訴 OpenCode：

  ```
  Fetch and follow instructions from https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.opencode/INSTALL.md
  ```

- 詳細文件：[docs/README.opencode.md](docs/README.opencode.md)

### Pi

從這個 repository 以 Pi 套件方式安裝 Superpowers：

```bash
pi install git:github.com/obra/superpowers
```

若要進行本機開發，可用這個 checkout 作為暫時套件載入來執行 Pi：

```bash
pi -e /path/to/superpowers
```

Pi 套件會載入 Superpowers 的 skill，以及一個小型擴充功能，在 session 啟動時、以及每次 compaction 之後，注入 `using-superpowers` bootstrap。Pi 原生支援 skill，所以不需要相容用的 `Skill` 工具。Subagent 與 task-list 工具則是選用的 Pi 附屬套件。

## 基本工作流程

1. **brainstorming** —— 在寫程式之前啟動。透過提問打磨粗略的想法、探索替代方案、分段呈現設計供你確認，並儲存設計文件。

2. **using-git-worktrees** —— 在設計獲得核可後啟動。在新分支上建立隔離的工作空間、執行專案設定、驗證測試基準(baseline)乾淨。

3. **writing-plans** —— 在設計核可後啟動。把工作拆成一口大小的任務(每個 2–5 分鐘)。每項任務都有明確的檔案路徑、完整的程式碼與驗證步驟。

4. **subagent-driven-development** 或 **executing-plans** —— 在有計畫後啟動。為每項任務派出全新的 subagent，並進行兩階段審查(先查 spec 合規、再查程式碼品質);或以批次方式執行，並在中間設人為 checkpoint。

5. **test-driven-development** —— 在實作期間啟動。強制執行 RED-GREEN-REFACTOR：先寫會失敗的測試、看它失敗、寫最小程式碼、看它通過、commit。在測試之前寫的程式碼會被刪除。

6. **requesting-code-review** —— 在任務之間啟動。對照計畫審查、依嚴重度回報問題。嚴重問題會擋住後續進度。

7. **finishing-a-development-branch** —— 在任務完成時啟動。驗證測試、呈現選項(merge／PR／保留／捨棄)、清理 worktree。

**agent 在任何任務之前都會先檢查有沒有相關的 skill。**這些是強制的工作流程，不是建議。

## 內含什麼

### Skill 函式庫

**Testing**
- **test-driven-development** —— RED-GREEN-REFACTOR 循環(含測試反模式 reference)

**Debugging**
- **systematic-debugging** —— 4 階段根本原因流程(含 root-cause-tracing、defense-in-depth、condition-based-waiting 等技巧)
- **verification-before-completion** —— 確認問題真的修好了

**Collaboration**
- **brainstorming** —— 蘇格拉底式的設計打磨
- **writing-plans** —— 詳細的實作計畫
- **executing-plans** —— 帶 checkpoint 的批次執行
- **dispatching-parallel-agents** —— 並行的 subagent 工作流程
- **requesting-code-review** —— 送審前的 checklist
- **receiving-code-review** —— 回應回饋
- **using-git-worktrees** —— 平行開發分支
- **finishing-a-development-branch** —— merge／PR 決策工作流程
- **subagent-driven-development** —— 以兩階段審查(先 spec 合規、再程式碼品質)快速迭代

**Meta**
- **writing-skills** —— 依最佳實務建立新 skill(含測試方法論)
- **using-superpowers** —— skill 系統的入門介紹

## 理念

- **Test-Driven Development** —— 永遠先寫測試
- **系統化優於臨時起意** —— 用流程，而非猜測
- **降低複雜度** —— 以簡單為首要目標
- **證據勝於宣稱** —— 宣告成功前先驗證

閱讀[最初的發布公告](https://blog.fsck.com/2025/10/09/superpowers/)。

## 貢獻

Superpowers 的一般貢獻流程如下。請留意：我們原則上不接受新增 skill 的貢獻，而且任何對 skill 的更新都必須在我們支援的所有 coding agent 上都能運作。

1. Fork 這個 repository
2. 切換到 'dev' 分支
3. 為你的工作建立一個分支
4. 依 `writing-skills` skill 來建立與測試新的或修改過的 skill
5. 送出 PR，並務必填完 pull request 範本

Skill 行為測試使用來自 [superpowers-evals](https://github.com/prime-radiant-inc/superpowers-evals/) 的 drill eval harness，clone 到 `evals/`——設定方式見 `evals/README.md`。外掛基礎設施測試位於 `tests/`，透過對應的 `run-*.sh` 或 `npm test` 執行。

完整指南見 `skills/writing-skills/SKILL.md`。

## 更新

Superpowers 的更新方式多少取決於 coding agent，但通常是自動的。

## 授權

MIT License —— 詳見 LICENSE 檔案。

## 視覺陪伴遙測(Visual companion telemetry)

因為 skill 與外掛不會給創作者任何回饋，我們完全不知道有多少人在用 Superpowers。預設情況下，brainstorming 的選用「視覺陪伴」功能上的 Prime Radiant 標誌，是從我們的網站載入的。它會包含正在使用的 Superpowers 版本，但不包含任何關於你的專案、prompt 或 coding agent 的細節。我們看不到你的點擊，也看不到任何你正在建立的東西。這讓我們能大略掌握有多少人在用 Superpowers、以及用的是哪個版本。它 100% 選用。若要關閉，把環境變數 `SUPERPOWERS_DISABLE_TELEMETRY` 設為任何 true 值即可。Superpowers 也尊重 Claude Code 的 `DISABLE_TELEMETRY` 與 `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` 這兩個退出選項。

## 社群

Superpowers 由 [Jesse Vincent](https://blog.fsck.com) 與 [Prime Radiant](https://primeradiant.com) 的其他夥伴一同打造。

- **Discord**：[加入我們](https://discord.gg/35wsABTejz)，取得社群支援、提問、分享你用 Superpowers 做出的東西
- **Issues**：https://github.com/obra/superpowers/issues
- **發布通知**：[訂閱](https://primeradiant.com/superpowers/)以取得新版本通知
