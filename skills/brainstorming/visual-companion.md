# Visual Companion Guide

以瀏覽器為基礎的視覺 brainstorming 陪伴工具，用來呈現 mockup、圖表與選項。

## 何時使用

逐題決定，不是逐 session 決定。判準：**使用者用「看」會不會比用「讀」更容易懂？**

當內容本身就是視覺性的，**用瀏覽器**：

- **UI mockup** —— wireframe、版面、導覽結構、元件設計
- **架構圖** —— 系統元件、資料流、關係圖
- **並排的視覺比較** —— 比較兩種版面、兩組配色、兩個設計方向
- **設計細修** —— 當問題是關於觀感、間距、視覺層次時
- **空間關係** —— 以圖表呈現的狀態機、流程圖、實體關係

當內容是文字或表格時，**用終端機**：

- **需求與範圍問題** —— 「X 是什麼意思？」「哪些功能在範圍內？」
- **概念性的 A/B/C 選擇** —— 在以文字描述的方案間挑選
- **取捨清單** —— 優缺點、比較表
- **技術決策** —— API 設計、資料建模、架構取向選擇
- **釐清問題** —— 任何答案是文字、而非視覺偏好的情況

一個*關於* UI 主題的問題，不會自動就是視覺問題。「你想要哪一種 wizard？」是概念性的 —— 用終端機。「這幾個 wizard 版面，哪個感覺對？」是視覺性的 —— 用瀏覽器。

## 運作方式

server 會監看一個目錄裡的 HTML 檔，並把最新的一個提供給瀏覽器。你把 HTML 內容寫進 `screen_dir`，使用者在瀏覽器裡看到它、並可點擊選取選項。選取結果會記錄到 `state_dir/events`，你在下一個回合讀取它。

**內容片段 vs 完整文件：**如果你的 HTML 檔以 `<!DOCTYPE` 或 `<html` 開頭，server 會原樣提供（只注入 helper script）。否則，server 會自動把你的內容包進 frame 模板 —— 加上頁首、CSS 主題、連線狀態，以及所有互動基礎設施。**預設寫內容片段。**只有在你需要對頁面有完整控制時才寫完整文件。

## 開始一段 session

```bash
# Start AFTER the user approves the companion. --open auto-opens their browser on
# the first screen; --project-dir persists mockups and enables same-port restart.
scripts/start-server.sh --project-dir /path/to/project --open

# Returns: {"type":"server-started","port":52341,
#           "url":"http://localhost:52341/?key=ab12…",
#           "screen_dir":"/path/to/project/.superpowers/brainstorm/12345-1706000000/content",
#           "state_dir":"/path/to/project/.superpowers/brainstorm/12345-1706000000/state"}
```

從回應裡把 `screen_dir` 與 `state_dir` 存下來。有了 `--open`，當你推出第一個畫面時瀏覽器會自己開啟 —— 你不需要請使用者去開它，但仍要把 URL 附上作為備援（headless／遠端環境不會自動開）。

**URL 內含一個 session key（`?key=…`）。**server 會拒絕任何沒帶它的請求，所以永遠把 `url` 欄位裡**完整**的 URL 給使用者 —— 絕不要把 query string 拿掉，也絕不要給出一個光禿禿的 `http://host:port`。這把 key 為 HTTP 與 WebSocket 的存取把關，好讓一個誤開的瀏覽器分頁、或網路上另一台機器，無法讀取那些畫面或注入事件。第一次載入之後，瀏覽器會用一個 cookie 記住這把 key，所以重新載入與 `/files/*` 資產不必再帶它就能運作。

**找出連線資訊：**server 會把它的啟動 JSON 寫到 `$STATE_DIR/server-info`。如果你在背景啟動 server、又沒有捕捉 stdout，讀那個檔就能取得 URL 與 port。使用 `--project-dir` 時，到 `<project>/.superpowers/brainstorm/` 找那段 session 的目錄。

**注意：**把專案根目錄當成 `--project-dir` 傳入，好讓 mockup 持久保存在 `.superpowers/brainstorm/`、並在 server 重啟後存活。沒有它，檔案會落在 `/tmp` 並被清掉。若 `.superpowers/` 還沒加進 `.gitignore`，提醒使用者加上去。

**依平台啟動 server：**

**Claude Code：**
```bash
# Default mode works — the script backgrounds the server itself.
scripts/start-server.sh --project-dir /path/to/project --open
```

在 Windows 上，這個 script 會自動偵測並切換到前景模式（那會擋住工具呼叫）。在 Bash 工具呼叫上用 `run_in_background: true`，好讓 server 跨對話回合存活，然後在下一個回合讀 `$STATE_DIR/server-info` 取得 URL 與 port。

**Codex：**
```bash
# Codex reaps background processes. The script auto-detects CODEX_CI and
# switches to foreground mode. Run it normally — no extra flags needed.
scripts/start-server.sh --project-dir /path/to/project --open
```

**Copilot CLI：**
```bash
# Use --foreground and start the server via the bash tool with mode: "async"
# so the process survives across turns. Capture the returned shellId for
# read_bash / stop_bash if you need to interact with it later.
scripts/start-server.sh --project-dir /path/to/project --open --foreground
```

**其他環境：**server 必須在背景跨對話回合持續執行。如果你的環境會回收 detach 的行程，用 `--foreground`，並以你平台的背景執行機制啟動該指令。

如果那個 URL 從你的瀏覽器無法連到（在遠端／容器化環境常見），綁定一個非 loopback 的 host：

```bash
scripts/start-server.sh \
  --project-dir /path/to/project \
  --host 0.0.0.0 \
  --url-host localhost
```

用 `--url-host` 控制回傳的 URL JSON 裡印出哪個 hostname。

## 迴圈

1. **確認 server 還活著**，然後把 **HTML 寫**到 `screen_dir` 裡的一個新檔：
   - **必要：在提到 URL 或推出畫面之前，先確認 server 還活著。**檢查 `$STATE_DIR/server-info` 存在、且 `$STATE_DIR/server-stopped` 不存在。如果它已經關閉，用 `start-server.sh` 搭**同一個 `--project-dir`** 重啟它 —— 它會重用同一個 port，所以使用者已開的分頁會自己重新連上（server 停擺期間它會顯示一個「paused」覆蓋層），你不必寄一個新 URL。server 在閒置 4 小時後會自動退出（可用 `--idle-timeout-minutes` 設定）。
   - 用語意化的檔名：`platform.html`、`visual-style.html`、`layout.html`
   - **絕不重用檔名** —— 每個畫面都用一個全新的檔
   - 用你的建檔工具 —— **絕不用 cat/heredoc**（會把雜訊倒進終端機）
   - server 會自動提供最新的檔

2. **告訴使用者可以期待什麼，然後結束你的回合：**
   - 提醒他們 URL（每一步都提，不只第一次）
   - 給一段簡短的文字摘要，說明畫面上有什麼（例如：「正在呈現首頁的 3 個版面選項」）
   - 請他們在終端機回覆：「看一下，讓我知道你的想法。想選的話點一下就能選取。」

3. **在你的下一個回合** —— 使用者在終端機回覆之後：
   - 若 `$STATE_DIR/events` 存在就讀它 —— 這裡以 JSON 行的形式記錄了使用者的瀏覽器互動（點擊、選取）
   - 與使用者的終端機文字合併，才能看到完整全貌
   - 終端機訊息是主要回饋；`state_dir/events` 提供結構化的互動資料

4. **迭代或前進** —— 如果回饋改變了目前畫面，就寫一個新檔（例如 `layout-v2.html`）。只有在目前這一步被確認後，才移到下一題。

5. **回到終端機時卸載** —— 當下一步不需要瀏覽器時（例如一個釐清問題、一段取捨討論），推出一個等待畫面來清掉過期的內容：

   ```html
   <!-- filename: waiting.html (or waiting-2.html, etc.) -->
   <div style="display:flex;align-items:center;justify-content:center;min-height:60vh">
     <p class="subtitle">Continuing in terminal...</p>
   </div>
   ```

   這可避免使用者盯著一個已經解決的選擇，而對話其實已經往前走了。當下一個視覺問題出現時，照常推出一個新的內容檔。

6. 重複直到完成。

## 撰寫內容片段

只寫進到頁面裡的那段內容。server 會自動把它包進 frame 模板（頁首、主題 CSS、連線狀態，以及所有互動基礎設施）。

**最小範例：**

```html
<h2>Which layout works better?</h2>
<p class="subtitle">Consider readability and visual hierarchy</p>

<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>Single Column</h3>
      <p>Clean, focused reading experience</p>
    </div>
  </div>
  <div class="option" data-choice="b" onclick="toggleSelect(this)">
    <div class="letter">B</div>
    <div class="content">
      <h3>Two Column</h3>
      <p>Sidebar navigation with main content</p>
    </div>
  </div>
</div>
```

就這樣。不需要 `<html>`、不需要 CSS、不需要 `<script>` 標籤。server 會把那些全部提供好。

## 可用的 CSS class

frame 模板為你的內容提供這些 CSS class：

### Options（A/B/C 選擇）

```html
<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>Title</h3>
      <p>Description</p>
    </div>
  </div>
</div>
```

**多選：**在容器上加 `data-multiselect`，讓使用者能選取多個選項。每次點擊會切換該項的選取樣式。

```html
<div class="options" data-multiselect>
  <!-- same option markup — users can select/deselect multiple -->
</div>
```

### Cards（視覺設計）

```html
<div class="cards">
  <div class="card" data-choice="design1" onclick="toggleSelect(this)">
    <div class="card-image"><!-- mockup content --></div>
    <div class="card-body">
      <h3>Name</h3>
      <p>Description</p>
    </div>
  </div>
</div>
```

### Mockup 容器

```html
<div class="mockup">
  <div class="mockup-header">Preview: Dashboard Layout</div>
  <div class="mockup-body"><!-- your mockup HTML --></div>
</div>
```

### Split view（並排）

```html
<div class="split">
  <div class="mockup"><!-- left --></div>
  <div class="mockup"><!-- right --></div>
</div>
```

### Pros/Cons

```html
<div class="pros-cons">
  <div class="pros"><h4>Pros</h4><ul><li>Benefit</li></ul></div>
  <div class="cons"><h4>Cons</h4><ul><li>Drawback</li></ul></div>
</div>
```

### Mock 元素（wireframe 的組成積木）

```html
<div class="mock-nav">Logo | Home | About | Contact</div>
<div style="display: flex;">
  <div class="mock-sidebar">Navigation</div>
  <div class="mock-content">Main content area</div>
</div>
<button class="mock-button">Action Button</button>
<input class="mock-input" placeholder="Input field">
<div class="placeholder">Placeholder area</div>
```

### 排版與區段

- `h2` —— 頁面標題
- `h3` —— 區段標題
- `.subtitle` —— 標題下方的次要文字
- `.section` —— 帶底部邊距的內容區塊
- `.label` —— 小寫的大寫標籤文字

## 瀏覽器事件格式

當使用者在瀏覽器裡點擊選項時，他們的互動會記錄到 `$STATE_DIR/events`（每行一個 JSON 物件）。當你推出一個新畫面時，這個檔會自動被清空。

```jsonl
{"type":"click","choice":"a","text":"Option A - Simple Layout","timestamp":1706000101}
{"type":"click","choice":"c","text":"Option C - Complex Grid","timestamp":1706000108}
{"type":"click","choice":"b","text":"Option B - Hybrid","timestamp":1706000115}
```

完整的事件串流顯示了使用者的探索路徑 —— 他們在定案前可能點過多個選項。最後一個 `choice` 事件通常是最終選擇，但點擊的樣態可能透露出值得追問的猶豫或偏好。

如果 `$STATE_DIR/events` 不存在，代表使用者沒有和瀏覽器互動 —— 只用他們的終端機文字。

## 設計提示

- **讓精細度與問題相稱** —— 版面問題用 wireframe，細修問題才做細修
- **在每一頁說明問題** —— 「哪個版面感覺比較專業？」而不是只有「選一個」
- **前進之前先迭代** —— 如果回饋改變了目前畫面，就寫一個新版本
- 每個畫面**最多 2–4 個選項**
- **在重要時用真實內容** —— 對一個攝影作品集，用真實影像（Unsplash）。placeholder 內容會遮掉設計問題。
- **讓 mockup 保持簡單** —— 聚焦在版面與結構，而非像素級完美的設計

## 檔案命名

- 用語意化的名稱：`platform.html`、`visual-style.html`、`layout.html`
- 絕不重用檔名 —— 每個畫面都必須是一個新檔
- 迭代時：附上版本後綴，像 `layout-v2.html`、`layout-v3.html`
- server 依修改時間提供最新的檔

## 清理

```bash
scripts/stop-server.sh $SESSION_DIR
```

如果這段 session 用了 `--project-dir`，mockup 檔會持久保存在 `.superpowers/brainstorm/` 供日後參考。只有 `/tmp` 的 session 會在停止時被刪除。

## Reference

- Frame 模板（CSS reference）：`scripts/frame-template.html`
- Helper script（client-side）：`scripts/helper.js`
