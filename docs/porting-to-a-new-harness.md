# Porting Superpowers to a New Harness

本指南說明如何為一個新的 harness——某個不是 Claude Code 的 IDE、CLI 或 agent runner——加入支援，讓 Superpowers 的 skill 在那裡也能像原生一樣自動觸發。

本文分兩層。**Part 1–3** 說明系統如何運作，以及如何判斷某個 harness 到底能不能被支援;動手之前請先讀這幾部分。**Part 4–8** 是給 agent（在你合作的人類使用者監督下）照著把移植從頭做到底、直到能發布的規範性程序。附錄則索引了目前的參考整合，方便你複製最接近的那一個。

整合機制因 harness 而異，而且會持續改變。本指南刻意教的是**不變式**——不論機制為何都必須成立的那些事——並指你去看一個活的參考實作來複製。當本指南與程式碼牴觸時，以程式碼為準;請回頭修指南。

## 動手之前

在這個 repo 裡，新增 harness 是風險最高的一種貢獻。動筆之前：

- 完整讀過 `CLAUDE.md` 與 `.github/PULL_REQUEST_TEMPLATE.md`——貢獻者規則與新 harness 的 PR 要求都不是選讀的。
- 在開啟**與已關閉**的 PR 中，搜尋是否有人先前嘗試過這個 harness。若有，先弄懂它為什麼卡住，再開始你自己的。

---

## Part 1 —— Superpowers 如何跨 harness 運作

Superpowers 到哪裡都是同一份內容。每個 harness 不同的，是那層把內容送到模型面前、並把它的指示翻譯成該 harness 原生工具的薄層。三個組成：

1. **Skill（與 harness 無關）。**`skills/` 裡的一切都是真理來源，由每個 harness 逐字共用。Skill 的寫法是描述*動作*——「invoke a skill」「read a file」「dispatch a subagent」「create a todo」——而從不指名某個特定工具。正是這一點，讓同一份 skill 本體不需修改就能跑在 Claude Code、Codex、Gemini、pi 及其餘 harness 上。

2. **工具對應（每個 harness 各一份）。**每個 harness 都需要把動作詞彙翻成它真實的工具名稱。這份翻譯放在 `skills/using-superpowers/references/<harness>-tools.md`，以及/或內嵌在該 harness 的 bootstrap injector 裡（見 Part 5）。它會寫，例如「*dispatch a subagent* → 呼叫 `task` 並帶 `subagent_type`」。

3. **Bootstrap（每個 harness 各一份）。**在每一段 session 的開頭，完整的 `skills/using-superpowers/SKILL.md` 都會被注入模型的 context，以 `<EXTREMELY_IMPORTANT>` 標籤包住，並附上工具對應。這個被注入的 skill，就是教模型「skill 是存在的」、以及「動作之前必須先檢查有沒有相關 skill」的東西。**bootstrap 就是整個整合。**沒有它，skill 檔就是死的——在磁碟上，卻永不被調用。

### 讓這一切成立的兩條規則

**1. Skill 命名的是動作，不是工具。****不要**為了配合你的 harness 而改動 skill 本體。移植是加上一份工具對應 reference 和一個 bootstrap injector;它從不伸手進 `skills/*/SKILL.md` 去替換工具名稱。（專案的貢獻者準則把 skill 內容視為經過細心調校、會塑造行為的程式碼;為了「合規」而重寫它，一眼就會被打回。）

**2. 一切都透過 harness 自己的安裝機制交付。絕不編輯使用者的檔案。**bootstrap、skill 與工具對應，全都是*作為 harness 所安裝之物的一部分*被交付——一個外掛、一個擴充、一筆市集項目、一個擴充內附的 context 檔。移植**絕不可**伸手進使用者的全域或個人設定（`~/.gemini/config/AGENTS.md`、`settings.json`、`trustedFolders.json`、手改的 `~/.bashrc` 等）去注入任何東西。harness 擁有它所載入的內容;你的安裝產物是你唯一能寫的東西。如果安裝機制實在無法承載 bootstrap，那是一個要浮上檯面的限制（Part 6）——絕不是動手改使用者設定的許可證。（Shape C *不是*例外：Gemini 的 context 檔沒問題，是因為它隨著*已安裝的擴充*一起出貨、並由 manifest 的 `contextFileName` 宣告——harness 載入的是擴充自己的檔，不是你在使用者家目錄裡改過的檔。）

---

## Part 2 —— 這個 harness 能被支援嗎？

一個 harness 唯有在能做到以下*所有*事情時，才能支援 Superpowers。寫程式之前先檢查這些——若第一項就過不了，停手。

### 硬性要求：session 開始時自動注入

harness 必須讓你能在**每一段 session 的開頭、且無需你合作的人類使用者逐 session 選擇加入**的情況下，把文字注入模型的 context。這是唯一不可妥協的能力。它可以是任何形式：

- 一個 **hook／事件系統**，在 session 開始時執行一道 shell 指令並讀取其 stdout（Claude Code、Cursor、Copilot CLI），或
- 一個帶 session-start 或訊息生命週期 callback 的**行程內外掛／擴充**，能改動訊息陣列（OpenCode、pi），或
- 一種**指示檔**慣例：harness 會載入一個*由你安裝的擴充所出貨並宣告*的 context 檔（例如 Gemini 的 `contextFileName` 指向擴充自己的 `GEMINI.md`）——而不是你在使用者家目錄裡改的檔。

如果讓 Superpowers 出現在模型面前的唯一辦法，是要你合作的人類使用者每段 session 都手動加入（貼一段 prompt、跑一道指令、啟用某個模式），那這個 harness **無法**被正確支援。Part 3 的驗收測試會失敗，PR 會被關閉。這是一個「移植」不算真正移植的最常見原因。

### 其餘的能力檢查表

| 能力 | 為何需要 | 若缺少 |
|---|---|---|
| **Skill 發現＋調用** | 模型必須能按需載入某個 skill 的完整內容 | 若沒有原生的 skill 工具，受認可的退路是直接 `read` 相關的 `SKILL.md`——見 Part 5。既沒有 skill 工具、也沒有檔案讀取的 harness，無法運作。 |
| **檔案讀／寫／編輯** | 幾乎每個 skill 都會操作檔案 | 必要。無替代方案。 |
| **執行 shell 指令** | TDD、驗證、git 流程 | 必要。 |
| **Subagent／task 派發** | `dispatching-parallel-agents`、`subagent-driven-development` | 可降級：若無法使用，那幾個特定 skill 會告訴模型改為 inline 完成工作、或回報缺少的能力——*絕不*叫它捏造一個 `Task` 呼叫。有些 harness 用一個 config flag 把這功能擋住（例如 Codex 需要啟用 multi-agent）。 |
| **Todo／task 追蹤** | 數個 skill 中的進度追蹤 | 可降級：退回用一個計畫檔或 `TODO.md`。 |
| **Web fetch／search** | 少數 skill | 可降級。 |
| **Shell 或多語 script 執行（Windows）** | 只有 shell-hook 形態、且你想支援 Windows 時才需要 | 見 Part 7。行程內外掛形態的 harness 完全繞開這一點。 |

「可降級」的意思是：該 skill 已經有針對缺少工具的退路措辭。你在工具對應裡的工作，是在真實工具存在時指向它、不存在時就沿用那套退路措辭。

### 你也許根本不需要一個新目錄

有些「新 harness」其實是換了個安裝器的既有整合。舉例來說，Factory 的 Droid 透過自己的 `plugin install` 指令消費 Claude Code 外掛，這裡不需要任何新檔。動手打造之前，先看看該 harness 能不能單純載入某個既有的 manifest。一個對這個 repo 只多加了 README 裡一段文字的移植，是完全好的結果。

---

## Part 3 —— 完成的定義

一個移植在以下*全部*成立時才算完成：

1. `using-superpowers` bootstrap 在每段 session 的開始時載入，每段皆然，無需逐 session 選擇加入。
2. 該 harness 有一份工具對應（在 `references/<harness>-tools.md`、內嵌於 bootstrap、或兩者皆有——依 Part 5）。
3. Skill 真的能被調用——原生地，或透過已載明的讀取 `SKILL.md` 退路——而且模型會遵循它們。
4. **驗收測試通過。**在一段乾淨的 session 裡，這則使用者訊息：

   > Let's make a react todo list

   *在任何程式碼被寫出之前*自動觸發 `brainstorming` skill。把完整逐字稿記下來——PR 需要它。
5. 測試涵蓋該整合（Part 5）並通過。
6. 真實使用者能透過 harness 自己的機制安裝它（不是手動複製檔案），且版本在適用處被記錄於 `.version-bump.json`（Part 6）。注意：有些安裝器會在安裝時改寫或剝除 manifest（其中一個把它降成只剩 `{"name": …}`），所以「*已安裝的*檔案回報 repo 版本」並非總是做得到——在來源 manifest 追蹤版本，別把被改寫的已安裝 manifest 當成失敗。

在完整驗收測試之前，先做個快速 smoke check：開一段 session，叫模型描述它的 superpowers。如果 bootstrap 有注入，它會知道自己有。（OpenCode 的安裝文件用 `opencode run --print-logs "hello" 2>&1 | grep -i superpowers` 以不同機制達到同樣目的——用 log-grep 而非問模型;`2>&1` 很重要，因為 log 走的是 stderr。找出你的 harness 的對應做法。）

---

## Part 4 —— 選擇你的整合形態

依*你如何把 bootstrap 送到模型面前*，結構上有三種形態。挑一個符合你的 harness 所提供之能力的，然後複製那個參考實作。形態幾乎決定了 Part 5 的一切——下面的步驟會依它分岔。

### 如何判斷你屬於哪個形態

在路由之前，先摸清 harness 的*實際*機制——別假設它有良好文件、也別假設它跟它 fork 的那個 harness 行為一樣。

**找出那個介面：**

- **上網搜尋該 harness 的文件**（extension／plugin／hook／skill／MCP／「context file」／「rules file」）。廠商工具變得很快;用搜尋，別信訓練知識。
- **找到並閱讀該 harness 的一個既有第三方 extension／plugin。**一個真實可用的範例勝過文件——它會顯示 manifest 形狀、安裝指令，以及該 harness 實際載入哪些元件。
- 看該 harness 在啟動時載入什麼：一個設定檔？一個 extensions 目錄？一個 per-project 或全域的指示檔（`AGENTS.md`、`<NAME>.md`）？

**如果它文件不足，就用經驗手段逆向它**（真實的移植者每一項都被迫做過）:

- `strings` 那個 binary／grep 安裝樹，找 hook 事件名稱、config 路徑，以及它讀取的指示檔。
- **叫執行中的模型列舉它自己的工具名稱**——例如「列出你能呼叫的每個工具的精確機器名稱」。這是不靠捏造就取得工具名稱的權威做法（見 Step 4）。
- 用一個**unique-marker 測試**證明每個假設：透過你以為有效的機制注入一個無意義 token，開一段全新 session，確認那個 token 真的抵達了模型。

**一個 fork 不會繼承它上游的行為。**一個衍生自另一個 harness（例如某個 Gemini 衍生 CLI）的 harness，可能暴露了上游的 manifest 欄位與 `@`-include 語法，*卻仍不以相同方式尊崇它們*。用 marker 驗證;絕不假設上游的配方會轉移過來。

然後路由到某個形態：

- session 開始時執行一道 shell 指令、其 stdout 會被讀取 → **Shape A**。
- 你能在其生命週期 callback 中跑程式碼的外掛／擴充模組 → **Shape B**。
- 只有一個永遠開著的指示檔、沒有 hook 也沒有程式碼外掛 → **Shape C**。

**形態可以組合——它們不是互斥的。**負責*skill 發現*的機制與負責*bootstrap*的機制不必是同一種形態——但**兩者都仍必須搭上安裝機制**（規則 2）。把兩個問題分開決定：*skill 在哪裡被發現？*以及*bootstrap 如何在每段 session 抵達模型？*一個 harness 可能透過外掛安裝 skill，卻需要 bootstrap 以另一種隨安裝出貨的方式交付（一個擴充宣告的 context 檔，或——見下——由 harness 在 session 開始時把已安裝的 `using-superpowers` skill 自己的 description 浮現出來）。如果不只一個安裝機制介面會自動注入，選最可靠的那個。你**不可以**做的，是靠編輯使用者的全域設定來橋接落差。

### Shape A —— Shell-hook

harness 有一個 hook 系統，在 session 開始時執行一道 shell 指令，並從其 stdout 讀取 JSON。被設定的指令執行 `run-hook.cmd`——一個多語 wrapper，它只負責定位 bash 並派發被指名的 script;那個 script（`hooks/session-start`，或某 harness 專屬的變體）才是讀取 `using-superpowers/SKILL.md` 並印出一個 JSON 物件、其**欄位名稱與巢狀每 harness 不同**的東西。

- 參考：`hooks/session-start`、`hooks/run-hook.cmd`，以及 per-harness 的 hook 設定 `hooks/hooks.json`（Claude Code）與 `hooks/hooks-cursor.json`（Cursor）。
- Manifest:`.cursor-plugin/plugin.json` 是 Shape A 的 manifest 範例，把 harness 指向 `./skills/` 與正確的 `hooks-*.json`。Claude Code 的 `.claude-plugin/plugin.json` 兩個欄位都不設——它靠慣例自動發現 `skills/` 與 `hooks/hooks.json`。**不要**為 Shape A 複製 Codex 的 `.codex-plugin/plugin.json`：它宣告了一個空的 `hooks` 物件，專門用來壓掉 Codex 對 `hooks/hooks.json` 的自動發現，因為 Codex 原生浮現 skill、且不跑 session-start hook。

> **一個 hook *系統*不等於一個 session-start *事件*。**一個 harness 可以有 `hooks.json` 機制——甚至在它的 binary 裡包含字面字串 `SessionStart`——卻沒有任何能在 session 開始時觸發、並能注入 context 的 hook 事件。（某個真實 harness 只暴露 pre/post-tool 與 stop 事件;那些 `SessionStart` 字串是遙測用的。）在投入 Shape A 之前，先確認你需要的那個*特定事件*存在、且能寫入模型的 context。若它做不到，bootstrap 該改放在指示檔（Shape C）裡。

### Shape B —— 行程內外掛／擴充

harness 載入一個暴露生命週期 callback 的 JS/TS 模組。你透過該 harness 的 API 註冊 skill 目錄，並以程式碼改動訊息陣列來注入 bootstrap。

- 參考：`.opencode/plugins/superpowers.js`（JavaScript）與 `.pi/extensions/superpowers.ts`（TypeScript）。pi 是任何**沒有原生 skill 工具**的 harness 最接近的參考。

### Shape C —— 指示檔

harness 既沒有 shell hook 也沒有程式碼外掛——它 session 開始時的介面是一個*由你安裝的擴充出貨、並由 manifest 宣告*的 context 檔（例如 Gemini 的 `contextFileName` → 擴充自己的 `GEMINI.md`）。你不能跑程式碼、也不能改動訊息;是擴充的 context 檔指向 bootstrap。這裡沒有 injector 去組裝字串或剝除 frontmatter——harness 原封不動地載入被引用的內容。**這之所以行得通，唯獨因為那個檔是已安裝擴充的一部分**——絕不可用「編輯使用者的全域 `GEMINI.md`／`AGENTS.md`」來取代出貨你自己的檔（規則 2）。

- 參考：`gemini-extension.json`（manifest，帶 `contextFileName`）、`GEMINI.md`（兩個 `@`-include——bootstrap skill 與工具對應 reference）、`skills/using-superpowers/references/gemini-tools.md`。
- 注意：`@`-include 是 Gemini 的功能。如果你的 harness 會載入一個指示檔卻沒有 include 語法，你必須改為把 bootstrap 內容 inline 進那個檔。
- **別相信 `@`-include 真的被展開——去證明它。**一個 Gemini *衍生*的 harness 可以接受 `@./path` 語法，卻把它當成*模型可以選擇去讀的提示*（它會發出一個 file-read 工具呼叫），而不是保證的 inline 展開。這就是「bootstrap 每段 session 都可靠地在場」與「模型也許會讀到它」之間的差別。跑一個 unique-marker 測試：若 marker 在*沒有*工具呼叫的情況下不在 context 裡，就**把內容 inline**，而不是用 `@`-include。

### 路由表

| 若該 harness… | 用形態 | 從哪複製 |
|---|---|---|
| 在 session 開始時執行一道 shell 指令並讀取其 stdout | A（shell-hook） | Cursor（`hooks/session-start` + `hooks/hooks-cursor.json` + `.cursor-plugin/`） |
| 是一個帶 session／訊息生命週期 callback 的 JS/TS 外掛宿主 | B（行程內） | OpenCode（`.opencode/`）——或 pi（`.pi/`），若它沒有原生 skill 工具 |
| 出貨一個它總是載入、由擴充宣告的 context 檔 | C（指示檔） | Gemini（`gemini-extension.json` + `GEMINI.md` + `references/gemini-tools.md`） |
| 有一個 plugin install 指令、且安裝器會保留 manifest 的 `contextFileName`（或等價物） | C，經由 plugin 安裝器 | Antigravity（`.antigravity-plugin/`——`agy plugin install` 出貨一個生成的 context 檔;驗證安裝器有保留它——Part 6） |

多數真實 harness 乾淨地落在某一列;最後一列是混合情形（規則 2 仍成立——bootstrap 搭安裝機制，絕不靠改使用者設定）。

---

## Part 5 —— 移植程序

### Step 1 —— 研讀最接近的參考實作

打開 Part 4 為你的形態指名的那些檔，從頭讀到尾。底下的樣式是摘要;程式碼才是規格。

### Step 2 —— 建立 manifest／進入點

建立該 harness 用來辨識外掛的任何東西。在精神上比照既有的：

- **Shape A:**一個 `*-plugin/plugin.json`（見 `.cursor-plugin/plugin.json`），帶 `name`、`version`、`description`、author/license/keywords、`"skills": "./skills/"`，以及 `"hooks": "./hooks/hooks-<harness>.json"`。再加 `hooks-<harness>.json` 本身，註冊一個 session-start hook、其指令調用 `run-hook.cmd`。
- **Shape B:**harness 載入的那個模組（例如 `.<harness>/plugins/*.js`），外加它被發現所需的任何 package metadata。被 commit 的 package metadata 是 **repo 根目錄的 `package.json`**:`main` 指向 OpenCode 外掛，`pi` 欄位（`pi.extensions`、`pi.skills`）加上 `pi-package` keyword 宣告 pi 擴充。Per-harness 的本機 manifest 與 lockfile 不進 git——`.opencode/.gitignore` 排除 `node_modules`、`package.json` 與 lockfile。對你的 harness 的*本機*安裝產物比照辦理，別讓它們汙染 repo——但絕不要 gitignore 掉 repo 根目錄的 `package.json`，它是被追蹤的真理來源。
  - **Build／相依性檢查。**決定該 harness 如何載入你的模組：它直接跑原始碼嗎（pi 的 `.ts` 在 `package.json` 裡被原樣引用;OpenCode 出貨純 `.js`），還是它需要一個 transpile／build 步驟？Superpowers 是零 runtime 相依。pi 的 `import type { ExtensionAPI }` 之所以行得通，正因為 harness 直接跑那個 `.ts`、在載入時供給該型別，且 repo 從不在 CI 對這檔做型別檢查——那個 import 甚至沒被宣告為相依。如果*你的* harness 真的會對外掛做型別檢查或打包，那就會爆：一個未宣告的型別 import 會失敗，而 PR 規則只為新 harness 開了*runtime* 相依的例外，不含 dev/型別套件。若你撞到這點，去跟維護者確認做法，別靜悄悄加一個相依。把任何 build 產物擋在 git 之外，並把指令寫進文件。
- **Shape C（指示檔）:**一個小 manifest（見 `gemini-extension.json`:`name`、`description`、`version`、`contextFileName`）加上 context 檔本身（`GEMINI.md` 就只是兩個 `@`-include:bootstrap skill 與工具對應 reference）。Gemini manifest 沒有 `skills` 欄位——Gemini 自動發現已安裝擴充裡所附的 `skills/` 目錄。如果你的 harness 有原生 skill 工具、卻沒有可註冊該目錄的 manifest 欄位，你必須找出它的發現慣例（讀它的擴充文件），然後用經驗驗證：接好之後，叫模型列出它可用的 skill——若所附的 skill 沒出現，發現就還沒生效。

### Step 3 —— 接上 bootstrap 注入

這是移植的核心。共同目標：在 session 開始時，把 `using-superpowers` skill 內容（以 `<EXTREMELY_IMPORTANT>` 標籤包住）加上該 harness 的工具對應送到模型面前，並附一句「此 skill 已啟用」讓模型別又去載入它。*怎麼*做——以及你組裝什麼、harness 原樣載入什麼——完全取決於你的形態。**不要**把一個形態的配方套到另一個形態。

**Shape A —— 一個 script 讀 `SKILL.md` 並印出該 harness 的 JSON。**被派發的 script（`hooks/session-start`）`cat` 整個 `SKILL.md`（含 frontmatter——沒關係;它被逐字印出），用「You have superpowers… for all other skills use the Skill tool」這段前言包住它、跳脫它，然後印出該 harness 的 JSON 形狀。Shape A 的工具對應**不**在這裡 inline——它住在 `references/<harness>-tools.md`（Step 4）。把 JSON 輸出形狀弄得完全正確。`hooks/session-start` 從環境變數偵測 harness，並印出*三種之一*的形狀：

- Cursor（`CURSOR_PLUGIN_ROOT` 有設）:`{ "additional_context": "…" }`
- Claude Code（`CLAUDE_PLUGIN_ROOT` 有設、`COPILOT_CLI` 沒設）:`{ "hookSpecificOutput": { "hookEventName": "SessionStart", "additionalContext": "…" } }`
- Copilot CLI／SDK 標準（其餘）:`{ "additionalContext": "…" }`

這是個陷阱。印錯欄位、或多印一個，都會讓 bootstrap 要嘛永不注入、要嘛注入兩次（Claude Code 會同時讀 `additional_context` 與 `hookSpecificOutput` 而不去重，所以兩者都印會雙重注入）。找出你的 harness 所預期的確切欄位、巢狀與事件 matcher 值。然後決定：在 `hooks/session-start` 加第四個分支，或——若該 harness 需要不同的 bootstrap 訊息或 env 契約——加一個專屬的 `hooks/session-start-<harness>` script。如果你加了一個分支、而你的 harness *也*設了某個前面分支所依據的 env var（有些 harness 也設 `CLAUDE_PLUGIN_ROOT`），把你的分支排在那個否則會蓋掉它的分支之前。比對該 harness 自己的事件 matcher 字串（Claude Code 用 `startup|clear|compact`,Cursor 用 `sessionStart`）;matcher 錯了，hook 就會靜悄悄永不觸發。

**hook-config 的 schema 本身每 harness 不同**——別假設 Claude Code 的形狀是通用的。比較 `hooks/hooks.json` 與 `hooks/hooks-cursor.json`:Cursor 的用 `"version": 1`、小寫的 `sessionStart` key、相對的 `./hooks/run-hook.cmd` 指令，並省略 Claude Code 用的 `matcher`/`type`/`async` 欄位。把你的 `hooks-<harness>.json` 比對到最接近的既有檔，而不是某個單一的正宗模板。

hook **指令字串引用一個 harness 提供的 plugin-root 變數**，而它的名字每 harness 不同：`hooks.json` 用 `${CLAUDE_PLUGIN_ROOT}`,`hooks-cursor.json` 用相對路徑。用你的 harness 所匯出的那個。（`session-start` script 會自己透過 `dirname` 重新推導根路徑，所以 script 本體不依賴這個——但 manifest 裡的指令依賴。）

**探明 harness 的契約。**上面那三個事實——env var、JSON 欄位／巢狀、matcher 字串——是該 harness 的契約，不是 Superpowers 的，所以你得自己去取得它們。讀該 harness 的 hook 文件，或用經驗查明：註冊一個丟棄式的 session-start hook，把它的環境倒出來並印一個 marker，然後觀察哪個 env var 標識了該 harness、以及該 harness 是否／如何攝取你的 stdout。在寫真正的分支之前，把這些釘死。

**Shape B —— 在程式碼裡組裝字串，然後以 user 訊息注入。**這裡你自己建 bootstrap：讀 `SKILL.md`、剝掉它的 YAML frontmatter，然後組裝 `<EXTREMELY_IMPORTANT>` + 一段短前言（說此 skill 已載入、不可再調用）+ 剝過的本體 + inline 的工具對應 + `</EXTREMELY_IMPORTANT>`。有個參考彼此不一致的細節：OpenCode 的前言說「do NOT use the skill tool…」（假設有一個 `skill` 工具存在），而 pi 的只說「do not try to load using-superpowers again」。如果你的 harness 沒有 skill 工具，用 pi 的措辭，別用 OpenCode 的。

把結果以 **user 角色訊息注入，而非 system 訊息**——system 訊息每回合重複時會膨脹 token（#750），而多個 system 訊息會弄壞某些模型（#894）。有三件事你必須複製：

- **去重防護。**生命週期 callback 可能反覆觸發（OpenCode 的 transform 在*每一個* agent 步驟都跑;pi 的 `context` 每回合觸發）。注入之前，檢查 bootstrap marker 是否已在場，若是就跳過。（參考挑了不同的 marker:pi 用一個自訂字串，OpenCode 用 `EXTREMELY_IMPORTANT` 標籤;比對那個標籤更穩健，因為它不需要 harness 專屬常數。）在模組層級快取 bootstrap 內容，免得每次呼叫都重讀、重解析 `SKILL.md`（#1202）。
- **Compaction。**如果 harness 會壓縮／摘要歷史，事後重新注入。pi 在 `session_start` 與 `session_compact` 設一個 `injectBootstrap` flag、在 `agent_end` 清掉它，並把訊息插在任何開頭的 compaction-summary 訊息*之後*。OpenCode 靠它的 per-step 重新注入加上去重防護。
- **訊息物件形狀每 harness 不同——探明你的，別複製字面。**兩個參考用的是*不相容*的形狀：pi 建 `{ role, content: [{ type, text }], timestamp }`;OpenCode 操作 `message.info.role` 與 `message.parts[]`。從它的 API 找出你的 harness 的訊息形狀;逐字複製某個參考的物件字面會靜悄悄失敗。

**Shape C —— 讓你的擴充的 context 檔指向 bootstrap;什麼都別組裝。**這裡沒有 injector，所以你*不*剝 frontmatter、也不建包好的字串。你的擴充所出貨的 context 檔（由 manifest 宣告——*不是*使用者自己的全域檔）拉進兩樣東西：`using-superpowers` skill 與該 harness 的工具對應 reference。`GEMINI.md` 用兩個 `@`-include 做到（`@./skills/using-superpowers/SKILL.md` 與 `@./skills/using-superpowers/references/<harness>-tools.md`）;harness 原樣載入它們、連 frontmatter 一起，而 `SKILL.md` 本身內部已經帶了它自己的 `<EXTREMELY-IMPORTANT>` 區塊。如果你的 harness 沒有 include 語法，改為把內容 inline 進指示檔。Gemini **不**出貨「已載入、別再調用」的前言——對一個 `@`-include harness 來說，那份內容就是啟用中的指示集，而不是模型會去重新載入的 skill。如果你發現你的 harness 真的會嘗試重新調用，就把那句話當成字面一行加進指示檔（你沒有其他任何辦法可以加它，因為沒有程式碼）。

### Step 4 —— 寫工具對應

把動作詞彙翻成該 harness 的真實工具。涵蓋以下每一個動作（只在真的不適用時才省略）:

- read a file
- create／edit／delete a file（一個 `apply_patch` 式的工具，還是分開的 write／edit？）
- run a shell command
- search file contents／find files by name（grep、glob）
- fetch a URL／web search
- **dispatch a subagent**，包括如何傳 agent 型別——以及啟用它所需的任何 config flag
- **create／update todos**（把較舊的 `TodoWrite` 引用當成這個動作）
- **invoke a skill**——見 Step 5

**從 harness 取得真實工具名稱;絕不捏造。**如果文件沒列出來，權威來源是 harness 本身：在一段活的 session 裡，叫模型「一行一個地列出你能呼叫的每個工具的精確機器名稱」，用它回報的。

**harness 如何找到 `skills/` 目錄，本身每 harness 不同**——確認它，別假設。可能是：一個 manifest 的 `skills` path 欄位（Codex 的 `"skills": "./skills/"`）;一個 harness 會自動掃描的*同層* `skills/`（此時 path 欄位會被*忽略*——某個真實 harness 只掃描貼在 `plugin.json` 旁邊的 `skills/`）;一個 API／註冊呼叫（OpenCode、pi）;或者你搭一個 install 目錄，把 manifest 與一個**指向 repo `skills/` 的 symlink** 配在一起，再把安裝器指向那個 staging 目錄（驗證安裝器有*解參考*該 symlink 並複製真實檔案——在依賴它之前，用 `agy plugin validate`／`install` 或等價物確認）。一個 `skills` path 欄位*不具*可攜性。

對應住在哪裡取決於形態：

- **Shape A:**放進 `skills/using-superpowers/references/<harness>-tools.md`。agent 從 bootstrap 抵達它——`SKILL.md` 的「Platform Adaptation」區段連到那些 per-harness reference 檔。（Shape A harness 沒有指示檔;對應*不*被 inline 進 hook 輸出。）
- **Shape B:**對應通常被 inline 進你注入的 bootstrap 字串（見 `superpowers.js` 裡的 `toolMapping` 常數）。pi 把它放在*兩處*——`piToolMapping()` inline **與** `references/pi-tools.md`。如果你在兩處維護它，兩處都要更新，否則移植只做了一半。
- **Shape C:**放進 `references/<harness>-tools.md`，並把它拉進那個總是載入的指示檔（例如 `GEMINI.md` 用 `@`-include 引入 `gemini-tools.md`）。

你也可以在 `SKILL.md` 的「Platform Adaptation」區段為你的 harness 加一行指路，好讓一個讀 bootstrap 的 agent 知道它的對應住在哪。這是一個移植*可以*對 `SKILL.md` 做的唯一編輯——而且只因為那個區段是一張指路清單，不是塑造行為的內容。它不違反「別編輯 skill 本體」規則（Part 1）;別碰任何 skill 裡的其他任何東西。（那張清單是個方便指路，不是完整登記冊——不是每個 harness 都被列出。）

### Step 5 —— 處理沒有原生 skill 工具的 harness

`using-superpowers/SKILL.md` 告訴模型*絕不要用檔案工具手動讀 skill 檔——永遠用你平台的 skill 載入機制*。重點是「別繞過那個機制」，不是「絕不使用 file-read」。什麼算「你平台的機制」取決於 harness——而對一個沒有 skill 工具的 harness，已載明的機制*就是*讀 `SKILL.md`。所以在那裡讀它是尊崇規則、而非破壞它。區分三種情形：

1. **原生 `Skill` 式工具**（Claude Code、Copilot CLI、Gemini 的 `activate_skill`）：把對應指向那個工具。
2. **有原生 skill *發現*但沒有 `Skill` 工具**（pi、Antigravity）:harness 能找到並列出 skill，但模型無法呼叫一個工具去載入某個。把 skill 裝到 harness 會掃描的地方（pi 透過 `resources_discover` → `skillPaths` 註冊;OpenCode 透過它的 `config` hook;`agy plugin install` 把它們複製進去），並告訴模型**在某個 skill 適用時，用 file-read 工具讀它的 `SKILL.md`** 來載入它——這是此處受認可的機制，正如 `references/pi-tools.md` 所述。

   **對 bootstrap 本身，優先用一個被宣告的 context 檔（Part 6）。**如果 harness 有一個 `contextFileName` 式的 manifest 欄位——如 Antigravity 就有——透過安裝器出貨一個生成的 context 檔：它保證被載入、並同時攜帶 `using-superpowers` 內容與工具對應。那是強而優先的路徑。

   **退路——被浮現的 skill 索引。**如果沒有 context-file 欄位、但 harness 在 session 開始時浮現每個已安裝 skill 的名稱＋description，你*既不*需要一個建好的索引、*也不*需要一道 runtime-list 指示——harness 就是索引，而 `using-superpowers` 自己被浮現的 description 可以正是觸發模型去載入它的東西。這比一個被宣告的 context 檔軟;相較於 context 檔／hook／行程內 injector，它有兩件事*不*給你——兩者都要顧到：
   - **它 bootstrap 的是*觸發*，不是*工具對應*。**一個 injector 每段 session 都把 `<harness>-tools.md` 與 `using-superpowers` 一起 prepend。這裡沒有東西注入對應——模型只看到 skill *description*，並且在需要工具名稱時必須去*讀*你的 `references/<harness>-tools.md`。它行得通是因為 skill 命名的是動作（模型動作時才讀對應），但這比注入軟。確保對應從模型會載入的東西可觸及——例如從 `SKILL.md` 的 Platform Adaptation 區段連過去、並與 skill 一起安裝——而不是只躺在 repo 裡。
   - **沒有結構上的保證觸發會發生。**沒有 `<EXTREMELY_IMPORTANT>` wrapper、沒有去重、沒有 compaction 後的重新注入——是否觸發取決於模型選擇對它在索引裡看到的一段 description 採取行動。這正是為什麼驗收測試在此處是強制的：它是*唯一*的保證，所以要在你的使用者真的會用的模型上跑它，不是只在最強的那個上。
3. **完全沒有 skill 系統：**沒有東西可註冊，而*唯一*的機制是模型按需讀 `SKILL.md`。但模型讀不了它找不到的東西：`using-superpowers/SKILL.md` **不**列舉可用的 skill，所以模型光靠自己不會知道有哪些 skill 存在、或它們的觸發條件。你必須供給一條發現路徑。兩個選項，而它們在耐久度上不同：（a） 生成一個 skill 索引（每個 `skills/*/SKILL.md` 的 `name` + `description` frontmatter），把它放進 `<EXTREMELY_IMPORTANT>` wrapper *裡面*、與工具對應並列（上面 Shape B 的配方），讓它被去重防護涵蓋——但一個建構期的索引會隨著 skill 被新增而過時;或 （b） 指示模型在 runtime 列出 `skills/*/SKILL.md` 並讀它們的 frontmatter 來找相符者——較慢但永不過時。優先選 （b），除非你有理由不。兩者皆無的話，一個無 skill 系統的移植會載入 bootstrap、卻靜悄悄地永不觸發任何其他 skill。

在情形 2 與 3 中，在你的工具對應裡明白說出：讀 `SKILL.md` 是被祝福的路徑，好讓模型不會以為自己在違反「絕不讀 skill 檔」規則。別在一個根本沒有 skill 系統的 harness 裡去找一個 `skillPaths` 式的註冊 API——情形 3 沒有那種東西。

### Step 6 —— 加測試

比照既有的 per-harness 測試風格：

- **Shape A:**斷言 hook 的 stdout 有你的 harness 所消費的確切 JSON 形狀、且它包含 bootstrap。見 `tests/hooks/test-session-start.sh`，它驗證每個 harness 的輸出形狀。
- **Shape B:**一個假造 harness 外掛 API 的單元測試，斷言生命週期 handler 有註冊、bootstrap 只注入一次、去重防護有效、以及（若相關）compaction 重新注入有效。見 `tests/pi/test-pi-extension.mjs`。以 `tests/opencode/` 的風格加一個隔離安裝的整合檢查。
- 如果 bootstrap 有被快取，測試檔案缺失時快取的行為（見 OpenCode 的快取測試）。

這些自動化測試涵蓋接線;Step 7 的活 tmux 執行才是證明整合真的會觸發 skill 的東西。

### Step 7 —— 在本機安裝，然後驅動一個活的實例來驗證

你無法靠讀程式碼確認一個移植可用。你得帶著你進行中的移植跑該 harness、盯著一段真實 session——這也正是你產出 PR 所需逐字稿的方式。

**在本機安裝。**把一個*本機*的 harness 實例指向你的工作樹，而非已發布的 build:

- **Shape A／C:**從這個 repo 的本機路徑安裝外掛／擴充（或把它的目錄 symlink 進 harness 會找的地方）。在它的文件裡找該 harness 的「從本機目錄／git checkout 安裝」路徑。
- **Shape B:**註冊本機模組——例如一筆指向本機路徑的 `opencode.json` `plugin` 項目，或讓 pi 從 repo 解析 `package.json` 欄位。

每次改動後重新安裝並重啟 harness，因為 bootstrap 在啟動時載入。

**用 tmux 驅動它。**多數 harness 是無法用管 stdin 驅動的互動式 REPL/TUI，所以把 harness 跑在一個 detach 的 tmux session 裡，用 `send-keys`／`capture-pane` 控制它。某個 harness 可能宣傳一種非互動的「跑一個 prompt」模式（例如 `opencode run "..."`）——為了快速 smoke check 試試它，但**別依賴它**：這些模式常常不穩、被 auth 擋、或被 trust 擋（某個真實 harness 的 `--print` 模式每次都卡住、無輸出地逾時）。要準備好*一切*——包括 smoke check——都透過 tmux 做。

**先清掉那些關卡，否則 tmux 會靜悄悄卡住。**許多 harness 會卡在首次執行的 onboarding、一個「你信任這個資料夾嗎？」提示、一個沙箱模式、或一個權限關卡上——而一個 detach 的 tmux session 只會就那樣坐著、毫無錯誤地等。執行前，先把你的暫存目錄預先設為信任（在 harness 的設定／config 裡），或準備好透過 `send-keys` 回答那些提示，並把 harness 的啟動時間算進你第一個 `sleep`。

```bash
# 1. Launch the harness detached, in a throwaway project dir
mkdir -p /tmp/port-smoke
tmux new-session -d -s port-test -c /tmp/port-smoke '<harness-launch-command>'

# 2. Let it initialize — real TUIs take longer than you think (10s+ with a model
#    handshake); tune this. THEN capture and clear any blocking modal before you
#    type a prompt: first-run onboarding and "trust this folder?" are modal, so
#    keystrokes sent during them select menu items instead of typing your prompt.
sleep 12
tmux capture-pane -t port-test -p          # onboarding / trust prompt? answer it via send-keys first
# (e.g. tmux send-keys -t port-test Enter   # to accept a trust prompt — inspect before assuming)

# 3. Smoke check: does the model know it has superpowers?
#    Send the text and Enter as SEPARATE send-keys with a beat between them —
#    sending them together races on some TUIs (Enter arrives before the text lands).
tmux send-keys -t port-test 'What are your superpowers?'; sleep 0.4; tmux send-keys -t port-test Enter
sleep 5
tmux capture-pane -t port-test -p          # reply should show it knows its skills

# 4. Acceptance test: exact prompt (note the escaped apostrophe), fresh session
tmux send-keys -t port-test 'Let'\''s make a react todo list'; sleep 0.4; tmux send-keys -t port-test Enter
# poll until the turn finishes — re-capture every few seconds, don't capture once
sleep 8
tmux capture-pane -t port-test -p          # PASS = brainstorming triggers BEFORE any code

# 5. Save the transcript for the PR, then clean up
tmux capture-pane -t port-test -p > /tmp/port-smoke/transcript.txt
tmux kill-session -t port-test
```

在這裡會咬人的 tmux 陷阱：啟動後、第一次 capture 之前要等;把 prompt 文字與 `Enter` 當成*分開*的 `send-keys` 呼叫、中間夾一個短 `sleep`（在某些 TUI 上一起送會 race），而 `Enter` 是一個 key 名稱、不是 `\n`;agent 的回合要花時間，所以要**在迴圈裡 poll `capture-pane`**、而不是只 capture 一次;`capture-pane` 只顯示可見的 pane，所以長對話要用 harness 自己的逐字稿／log 檔當作真理紀錄;做完永遠 `kill-session`。

如果 smoke check 顯示模型*不*知道它有 superpowers，那 bootstrap 就是沒載入——先修那個，再去操心驗收測試。

---

## Part 6 —— 發布與釋出

一個在這個 repo 裡可用的整合，在真實使用者能安裝它之前都還不算可用。發布方式每個 harness 生態各異——找出你的：

| 通路 | 範例 | 你要做什麼 |
|---|---|---|
| 原生外掛市集 | Claude Code | 在 `.claude-plugin/marketplace.json` 註冊;使用者 `/plugin install`。外部的 `superpowers-marketplace` repo 是使用者安裝的真理來源——見 `CLAUDE.md` 裡的釋出步驟。 |
| 外部市集 fork、以 script 同步 | Codex | `scripts/sync-to-codex-plugin.sh` 把被追蹤的外掛檔 rsync 進一個獨立的 fork repo 並開 PR。讀它的 include/exclude 清單，好出貨正確的樹（它刻意丟掉 repo 內部目錄與其他 harness 的 dotdir）。 |
| Git-URL 擴充安裝 | Gemini、Kimi Code、OpenCode | 使用者從一個 git URL 安裝（`gemini extensions install …`;Kimi Code `/plugins install …`;一筆 `opencode.json` `plugin` 陣列項目）。把確切指令寫進文件。 |
| Package-manifest 欄位 | pi | 透過 repo 根目錄 `package.json` 裡的欄位宣告;使用者用 harness 的 package 指令安裝。 |
| 本機安裝器（plugin install） | Antigravity（`agy`） | 一個小的 `install.sh`，對一個裝著 manifest、skill 與一個生成的 `contextFileName` context 檔（即 bootstrap）的 staging 目錄執行 harness 自己的 `agy plugin install`。一切都透過安裝機制抵達——*不是*靠編輯使用者的設定（見下）。 |

然後：

- **一個外掛安裝器可能靜悄悄地剝除*未宣告*的檔案——所以把 bootstrap 做成安裝器*認得*的檔，絕不做成一個使用者設定的編輯。**一次 `plugin install` 通常只複製它認得的元件（skills/agents/commands/mcp/hooks/context），並丟棄其餘一切，所以一個 manifest 沒宣告的 context 檔就這樣從安裝裡消失。修法**不是**放棄、然後寫進使用者的設定（**規則 2**）——而是把 bootstrap 宣告成一個被認得的元件。依升級順序：
  - **出貨一個 manifest 宣告的 context 檔。**如果 harness 有一個 `contextFileName` 式的欄位（一個擴充宣告、它每段 session 都載入的檔），那是最強的乾淨 bootstrap：宣告它，安裝器會保留它、*而且* harness 會載入它。在安裝時從活的 `using-superpowers/SKILL.md` + 工具對應（包在 `<EXTREMELY_IMPORTANT>` 裡）生成它，好讓已安裝的 bootstrap 永不漂移。這正是 `.antigravity-plugin/install.sh` 做的事——`agy plugin install` 回報 `✔ context : ANTIGRAVITY.md`，而一段乾淨 session 會讀 `using-superpowers` 的 SKILL.md、載入 `brainstorming`，並在任何程式碼之前進入 brainstorming 流程。**用一個 marker 驗證**安裝器保留了那個檔、且 harness 載入了它：某位移植者錯誤地下結論說它做不到，因為他們出貨了那個檔卻*沒*宣告 `contextFileName`，於是它被當成不被認得而剝除。
  - **否則就倚靠已安裝的 `using-superpowers` skill 本身。**如果 harness 在 session 開始時浮現每個已安裝 skill 的名稱＋description,`using-superpowers` 的 description（「Use when starting any conversation…」）可以促使模型去載入它——安裝那個 skill *就是* bootstrap。較軟（沒有保證的 wrapper;它攜帶觸發、但不攜帶工具對應——見 Step 5），所以在可用時優先選被宣告的 context 檔。
  - 兩者都不行的話，這個 harness 就還無法被乾淨地支援——**說出來**並提報，而不是動手改使用者的設定。

- **寫安裝文件。**一個 `docs/README.<harness>.md`，以及/或一個 `.<harness>/INSTALL.md`（見 `docs/README.opencode.md` 與 `.opencode/INSTALL.md`），外加 top-level `README.md` 裡的一段安裝說明。唯一受支援的安裝動作是**執行 harness 自己的安裝指令**（`agy plugin install`、`gemini extensions install`、`/plugin install` 等）。手動複製 skill 檔與編輯使用者的全域／個人設定*兩者*都禁止（規則 2／PR 規則）。如果該 harness 根本沒有安裝指令——它唯一的介面是一個使用者擁有的設定檔——那它就過不了「透過安裝機制交付」規則，你應該提報這件事，而不是出貨一個會編輯使用者檔案的安裝器。
- **登記版本。**如果你的 harness 引入一個*新的*帶版本 manifest，把它的路徑與版本欄位加進 `.version-bump.json`，好讓 `scripts/bump-version.sh` 讓它們保持同步（讀那個檔看目前追蹤了什麼）。一個沒登記在那裡的新 manifest 會出貨過時的版本。如果你的 harness 改為搭一個已被追蹤的檔——pi 在 repo 根目錄 `package.json` 裡宣告自己，而那已被列出——就沒有新東西要加。
- **如果沒有既有通路合適，你就是在立一個新的。**那四列可能沒有一列符合你的 harness。如果它需要一個 Codex 式的外部 fork 同步，`scripts/sync-to-codex-plugin.sh` 是要複製的模板（注意它錨定的 include/exclude 清單與它的 PR 自動化）。而每當你新增一個 per-harness 目錄，把它加進*其他* harness 的同步 exclude（例如 `sync-to-codex-plugin.sh` 裡的 EXCLUDES 清單），好讓你的 dotdir 不會漏進它們的發布。

---

## Part 7 —— 跨平台／Windows

只跟 shell-hook 形態相關。`hooks/run-hook.cmd` 是個多語檔：一個同時作為 Windows 批次 script 與 Unix shell script 都有效的單一檔案。在 Windows 上，`cmd.exe` 跑批次部分，它定位 `bash`（Git for Windows，再來是 PATH 上的 `bash`）並跑被指名的 hook script;若找不到 bash，它乾淨地退出，好讓 harness 仍能運作、只是沒有注入。在 Unix 上，開頭的 `:` 讓批次區塊成為 no-op,shell 直接跑 script。

這強制了兩條你必須遵守的規則：

- **Hook script 不帶副檔名**（`session-start`，不是 `session-start.sh`）。Claude Code 的 Windows 處理會在任何含 `.sh` 的指令前面加上 `bash`，那會雙重調用。把你的 hook script 命名成不帶副檔名。
- 別為 hook script 寫 per-OS 變體。一個不帶副檔名的 bash script 加上多語 wrapper 就涵蓋所有三個平台。

`hooks/run-hook.cmd` 本身是權威實作——去讀它。見 `docs/windows/polyglot-hooks.md`，那裡有這個 dispatcher 樣式背後的背景與理由。

---

## Part 8 —— 送出 PR

- 目標對準 **`dev`** 分支。一個 PR 一個 harness。
- 填好 PR 範本的 **「New harness support」** 區段，並貼上完整的驗收測試逐字稿（那段顯示 `brainstorming` 自動觸發的「Let's make a react todo list」session）。少了這份證明的 PR 會被關閉。
- Superpowers 是一個零相依外掛。別加第三方 runtime 相依。新增一個 harness 是貢獻者規則所允許的唯一例外，即便如此也只限整合嚴格需要的——編譯後會消失的 type-only import 沒問題;runtime 套件不行。
- 別碰 skill 本體（Part 1）。如果你發現自己為了讓移植可用而在編輯某個 `SKILL.md`，那個修法該放進你的工具對應。

---

## Appendix A —— 參考整合（現行）

把這當作活的索引;拿不準時，去讀那些檔，別讀這張表。

| Harness | 進入點 | Bootstrap 機制 | 工具對應 | 測試 | 發布 |
|---|---|---|---|---|---|
| Claude Code | `.claude-plugin/plugin.json` + `hooks/hooks.json` | shell hook → `hooks/session-start`（`hookSpecificOutput.additionalContext`） | 原生 `Skill` 工具;`references/claude-code-tools.md` | `tests/hooks/` | 市集 |
| Codex | `.codex-plugin/plugin.json`（宣告空的 `hooks`） | 原生 skill 發現（無 session-start hook） | `references/codex-tools.md` | `tests/codex/`、`tests/codex-plugin-sync/` | fork 同步（`scripts/sync-to-codex-plugin.sh`） |
| Cursor | `.cursor-plugin/plugin.json` + `hooks/hooks-cursor.json` | shell hook → `hooks/session-start`（`additional_context`） | `references/claude-code-tools.md` | `tests/hooks/` | 手寫 |
| Copilot CLI | （共用 Claude Code 的 hook 路徑;`COPILOT_CLI` env） | shell hook → `hooks/session-start`（`additionalContext`） | `references/copilot-tools.md` | `tests/hooks/` | —— |
| Gemini CLI | `gemini-extension.json` + `GEMINI.md` | 指示檔 `@`-include bootstrap + 對應 | `references/gemini-tools.md` | —— | `gemini extensions install` |
| Kimi Code | `.kimi-plugin/plugin.json` | manifest `sessionStart.skill` 載入 `using-superpowers` | manifest 內 inline 的 `skillInstructions` | `tests/kimi/` | 市集或 `/plugins install` GitHub URL |
| OpenCode | `.opencode/plugins/superpowers.js`（經由根 `package.json` `main` 宣告） | 行程內：`config` hook 註冊 skill 目錄;`experimental.chat.messages.transform` 注入 user 訊息 | `superpowers.js` 內 inline | `tests/opencode/` | `opencode.json` plugin git URL |
| pi | `.pi/extensions/superpowers.ts` | 行程內：`resources_discover` 註冊 skill;`context` 事件注入 user 訊息;帶 lifecycle-flag 與 compaction 感知 | `piToolMapping()` inline **與** `references/pi-tools.md` | `tests/pi/` | repo 根目錄 `package.json` 欄位 |

## Appendix B —— 咬過移植者的陷阱

- **選擇加入不是移植。**如果你合作的人類使用者每段 session 都得做點什麼才能拿到 Superpowers，驗收測試就失敗。重讀 Part 2。
- **JSON 欄位錯 → 靜默失敗或雙重注入。**只限 Shape A。確認確切的欄位／巢狀;Claude Code 會不去重地讀兩個欄位。
- **Hook-config schema 每 harness 不同。**Shape A。Cursor 的 `hooks-cursor.json` 跟 Claude Code 的長得完全不像（`version`、小寫 `sessionStart`、相對指令、無 `matcher`/`type`/`async`）。比對最接近的既有檔。
- **Plugin-root env var 每 harness 不同。**Shape A。hook 指令用 `${CLAUDE_PLUGIN_ROOT}`（Claude）或相對路徑（Cursor）。用你的 harness 所匯出的;script 會自己重新推導根路徑。
- **System-message 注入。**Shape B 刻意注入一個 *user* 訊息（#750、#894）。別把它「修」成 system 訊息。
- **Per-step 對 per-turn callback。**OpenCode 每一步觸發（per-call 去重防護）;pi 每回合觸發（lifecycle flag + `agent_end` 重設）。把一個 harness 的去重策略複製到另一個的 callback 頻率上會弄壞注入。
- **訊息物件形狀每 harness 不同。**Shape B。pi 與 OpenCode 用不相容的形狀;探明你的，別複製某個參考的物件字面。
- **去找一個不存在的 skill-註冊 API。**一個沒有 skill 系統（不只是沒有 `Skill` 工具）的 harness，沒有東西可註冊——模型按需讀 `SKILL.md`。別假設有一個 `skillPaths` 等價物存在。
- **對應在兩處。**對行程內外掛，對應可能同時住在 inline 與一個 `references/` 檔（pi）。兩處都更新。
- **那句「絕不讀 skill 檔」。**它的意思是「別繞過你平台的 skill 載入機制」，不是「絕不使用 file-read」。在一個沒有 skill 工具的 harness 上，那個機制*就是*讀 `SKILL.md`——在對應裡明白說出來（Part 5）。
- **Windows 上的 `.sh`。**讓 hook script 不帶副檔名（Part 7）。
- **未登記的版本。**一個沒加進 `.version-bump.json` 的新 manifest 會出貨過時版本（Part 6）。
- **為了配合 harness 而編輯 skill。**絕不。修法放進工具對應。
