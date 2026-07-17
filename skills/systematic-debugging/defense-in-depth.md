# 縱深防禦驗證（Defense-in-Depth Validation）

## 概觀

當你修好一個由無效資料造成的 bug 時，在單一處加上驗證感覺就夠了。但那個單一檢查可能被不同的程式碼路徑、重構，或 mock 繞過。

**核心原則：**在資料經過的「每一層」都驗證。讓 bug 在結構上不可能發生。

## 為何要多層

單一驗證：「我們修好了 bug」
多層：「我們讓 bug 不可能發生」

不同的層攔截不同的情況：
- 進入點驗證攔截大多數 bug
- 業務邏輯攔截邊界情況
- 環境 guard 防止特定情境下的危險
- Debug logging 在其他層失效時提供幫助

## 四層

### Layer 1：進入點驗證
**目的：**在 API 邊界拒絕明顯無效的輸入

```typescript
function createProject(name: string, workingDirectory: string) {
  if (!workingDirectory || workingDirectory.trim() === '') {
    throw new Error('workingDirectory cannot be empty');
  }
  if (!existsSync(workingDirectory)) {
    throw new Error(`workingDirectory does not exist: ${workingDirectory}`);
  }
  if (!statSync(workingDirectory).isDirectory()) {
    throw new Error(`workingDirectory is not a directory: ${workingDirectory}`);
  }
  // ... proceed
}
```

### Layer 2：業務邏輯驗證
**目的：**確保資料對這個操作而言合理

```typescript
function initializeWorkspace(projectDir: string, sessionId: string) {
  if (!projectDir) {
    throw new Error('projectDir required for workspace initialization');
  }
  // ... proceed
}
```

### Layer 3：環境 Guard
**目的：**在特定情境中防止危險操作

```typescript
async function gitInit(directory: string) {
  // In tests, refuse git init outside temp directories
  if (process.env.NODE_ENV === 'test') {
    const normalized = normalize(resolve(directory));
    const tmpDir = normalize(resolve(tmpdir()));

    if (!normalized.startsWith(tmpDir)) {
      throw new Error(
        `Refusing git init outside temp dir during tests: ${directory}`
      );
    }
  }
  // ... proceed
}
```

### Layer 4：Debug Instrumentation
**目的：**擷取 context 供事後鑑識

```typescript
async function gitInit(directory: string) {
  const stack = new Error().stack;
  logger.debug('About to git init', {
    directory,
    cwd: process.cwd(),
    stack,
  });
  // ... proceed
}
```

## 套用這個模式

當你找到一個 bug 時：

1. **追蹤資料流** - 壞值從哪裡來？在哪裡被使用？
2. **標出所有檢查點** - 列出資料經過的每一個點
3. **在每一層加上驗證** - 進入點、業務、環境、debug
4. **測試每一層** - 試著繞過 layer 1，驗證 layer 2 會攔到它

## 來自 Session 的範例

Bug：空的 `projectDir` 導致 `git init` 在原始碼中執行

**資料流：**
1. 測試 setup → 空字串
2. `Project.create(name, '')`
3. `WorkspaceManager.createWorkspace('')`
4. `git init` 在 `process.cwd()` 執行

**加上的四層：**
- Layer 1：`Project.create()` 驗證非空／存在／可寫
- Layer 2：`WorkspaceManager` 驗證 projectDir 不為空
- Layer 3：`WorktreeManager` 在測試中拒絕在 tmpdir 之外執行 git init
- Layer 4：git init 之前記錄 stack trace

**結果：**全部 1847 個測試通過，bug 不可能重現

## 關鍵洞見

四層全都是必要的。測試期間，每一層都攔到了其他層漏掉的 bug:
- 不同的程式碼路徑繞過了進入點驗證
- Mock 繞過了業務邏輯檢查
- 不同平台上的邊界情況需要環境 guard
- Debug logging 辨識出結構性的誤用

**不要停在單一驗證點。**在每一層都加上檢查。
