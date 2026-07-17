# 測試反模式（Testing Anti-Patterns）

**在下列時機載入這份 reference：**撰寫或修改測試、加入 mock，或想在正式程式碼中加入只給測試用的 method 時。

## 概觀

測試必須驗證真實行為，而非 mock 的行為。Mock 是用來隔離的手段，不是被測試的對象。

**核心原則：**測程式碼做了什麼，不是測 mock 做了什麼。

**嚴格遵循 TDD 可以避免這些反模式。**

## 鐵律（The Iron Laws）

```
1. 絕對不要（NEVER）測試 mock 的行為
2. 絕對不要（NEVER）在正式類別中加入只給測試用的 method
3. 絕對不要（NEVER）在不了解相依關係的情況下 mock
```

## 反模式 1：測試 Mock 的行為

**違規寫法：**
```typescript
// ❌ BAD: Testing that the mock exists
test('renders sidebar', () => {
  render(<Page />);
  expect(screen.getByTestId('sidebar-mock')).toBeInTheDocument();
});
```

**為何這是錯的：**
- 你驗證的是 mock 能動，而不是元件能動
- mock 在場時測試通過，不在場時失敗
- 完全沒告訴你真實行為如何

**你合作的人類使用者的糾正：**「我們是在測一個 mock 的行為嗎？」

**修正：**
```typescript
// ✅ GOOD: Test real component or don't mock it
test('renders sidebar', () => {
  render(<Page />);  // Don't mock sidebar
  expect(screen.getByRole('navigation')).toBeInTheDocument();
});

// OR if sidebar must be mocked for isolation:
// Don't assert on the mock - test Page's behavior with sidebar present
```

### Gate Function

```
在對任何 mock 元素做 assertion 之前：
  問：「我測的是真實元件行為,還是只是 mock 存在？」

  若是在測 mock 是否存在：
    STOP——刪掉這個 assertion,或把該元件 unmock

  改為測試真實行為
```

## 反模式 2：正式程式碼中只給測試用的 Method

**違規寫法：**
```typescript
// ❌ BAD: destroy() only used in tests
class Session {
  async destroy() {  // Looks like production API!
    await this._workspaceManager?.destroyWorkspace(this.id);
    // ... cleanup
  }
}

// In tests
afterEach(() => session.destroy());
```

**為何這是錯的：**
- 正式類別被只給測試用的程式碼污染
- 若在正式環境不小心呼叫會很危險
- 違反 YAGNI 與關注點分離
- 混淆了物件生命週期與實體生命週期

**修正：**
```typescript
// ✅ GOOD: Test utilities handle test cleanup
// Session has no destroy() - it's stateless in production

// In test-utils/
export async function cleanupSession(session: Session) {
  const workspace = session.getWorkspaceInfo();
  if (workspace) {
    await workspaceManager.destroyWorkspace(workspace.id);
  }
}

// In tests
afterEach(() => cleanupSession(session));
```

### Gate Function

```
在對正式類別加入任何 method 之前：
  問：「這只有測試會用到嗎？」

  若是：
    STOP——不要加它
    改放到測試工具（test utilities）裡

  問：「這個類別擁有這項資源的生命週期嗎？」

  若否：
    STOP——這個 method 放錯類別了
```

## 反模式 3：在不了解的情況下 Mock

**違規寫法：**
```typescript
// ❌ BAD: Mock breaks test logic
test('detects duplicate server', () => {
  // Mock prevents config write that test depends on!
  vi.mock('ToolCatalog', () => ({
    discoverAndCacheTools: vi.fn().mockResolvedValue(undefined)
  }));

  await addServer(config);
  await addServer(config);  // Should throw - but won't!
});
```

**為何這是錯的：**
- 被 mock 的 method 有測試所依賴的副作用（寫入 config）
- 為了「保險」而過度 mock，反而破壞了實際行為
- 測試因錯誤的理由通過，或神秘地失敗

**修正：**
```typescript
// ✅ GOOD: Mock at correct level
test('detects duplicate server', () => {
  // Mock the slow part, preserve behavior test needs
  vi.mock('MCPServerManager'); // Just mock slow server startup

  await addServer(config);  // Config written
  await addServer(config);  // Duplicate detected ✓
});
```

### Gate Function

```
在 mock 任何 method 之前：
  STOP——先不要 mock

  1. 問：「真正的 method 有哪些副作用？」
  2. 問：「這個測試依賴其中任何一個副作用嗎？」
  3. 問：「我是否完全理解這個測試需要什麼？」

  若依賴副作用：
    在更低的層級 mock（實際的慢／外部操作）
    或使用能保留必要行為的 test double
    而不是測試所依賴的高階 method

  若不確定測試依賴什麼：
    先用真實實作跑一次測試
    觀察實際上需要發生什麼
    再在正確層級加入最少量的 mock

  警訊：
    - 「我先 mock 這個以防萬一」
    - 「這可能很慢,還是 mock 掉好了」
    - 在不了解相依鏈的情況下 mock
```

## 反模式 4：不完整的 Mock

**違規寫法：**
```typescript
// ❌ BAD: Partial mock - only fields you think you need
const mockResponse = {
  status: 'success',
  data: { userId: '123', name: 'Alice' }
  // Missing: metadata that downstream code uses
};

// Later: breaks when code accesses response.metadata.requestId
```

**為何這是錯的：**
- **部分 mock 會隱藏結構性假設**——你只 mock 了你知道的欄位
- **下游程式碼可能依賴你沒放進去的欄位**——無聲失敗
- **測試通過但整合失敗**——mock 不完整，真實 API 完整
- **虛假的信心**——測試對真實行為什麼都證明不了

**鐵則（The Iron Rule）：**照現實中存在的樣子 mock **完整**的資料結構，而不是只放你當下測試會用到的欄位。

**修正：**
```typescript
// ✅ GOOD: Mirror real API completeness
const mockResponse = {
  status: 'success',
  data: { userId: '123', name: 'Alice' },
  metadata: { requestId: 'req-789', timestamp: 1234567890 }
  // All fields real API returns
};
```

### Gate Function

```
在建立 mock 回應之前：
  檢查：「真實 API 回應包含哪些欄位？」

  行動：
    1. 從文件／範例查看實際的 API 回應
    2. 納入系統下游可能會用到的所有欄位
    3. 確認 mock 完整符合真實回應的 schema

  關鍵：
    如果你要建立 mock,就必須理解整個結構
    當程式碼依賴被省略的欄位時,部分 mock 會無聲失敗

  若不確定：納入所有文件中記載的欄位
```

## 反模式 5：把整合測試當事後補的事

**違規寫法：**
```
✅ Implementation complete
❌ No tests written
"Ready for testing"
```

**為何這是錯的：**
- 測試是實作的一部分，不是可有可無的後續
- TDD 本來會抓到這個
- 沒有測試就不能宣稱完成

**修正：**
```
TDD cycle:
1. Write failing test
2. Implement to pass
3. Refactor
4. THEN claim complete
```

## 當 Mock 變得太複雜

**警訊：**
- mock 的 setup 比測試邏輯還長
- 為了讓測試通過而什麼都 mock
- mock 缺少真實元件擁有的 method
- mock 一改動，測試就壞

**你合作的人類使用者的提問：**「這裡我們真的需要用 mock 嗎？」

**考慮：**用真實元件的整合測試，往往比複雜的 mock 更簡單

## TDD 可避免這些反模式

**為何 TDD 有幫助：**
1. **先寫測試** → 強迫你思考你到底在測什麼
2. **看著它失敗** → 確認測試測的是真實行為，而非 mock
3. **最小實作** → 只給測試用的 method 不會偷偷混進來
4. **真實相依** → 在 mock 之前，你就看到了測試實際需要什麼

**如果你在測 mock 的行為，你就違反了 TDD**——你在還沒看著測試對真實程式碼失敗之前就加了 mock。

## 快速參考

| 反模式 | 修正 |
|--------------|-----|
| 對 mock 元素做 assertion | 測真實元件，或把它 unmock |
| 正式程式碼中只給測試用的 method | 移到測試工具 |
| 不了解就 mock | 先理解相依，再最小化 mock |
| 不完整的 mock | 完整鏡射真實 API |
| 測試當事後補 | TDD——測試先行 |
| 過度複雜的 mock | 考慮改用整合測試 |

## 警訊（Red Flags）

- assertion 檢查 `*-mock` 之類的 test ID
- 只有測試檔會呼叫的 method
- mock setup 佔測試的 50% 以上
- 移除 mock 後測試就 fail
- 說不出為什麼需要這個 mock
- 「以防萬一」而 mock

## 底線

**Mock 是用來隔離的工具，不是拿來測試的東西。**

如果 TDD 揭露你在測 mock 的行為，你就走錯路了。

修正：測試真實行為，或反問自己到底為什麼要 mock。
